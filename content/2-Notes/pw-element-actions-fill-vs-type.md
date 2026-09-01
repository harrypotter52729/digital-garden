---
id: 202608152009
title: Element Action Methods: fill() vs Deprecated type()
aliases:
  - fill vs type
  - fill method
  - type method deprecated
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Element Action Methods: fill() vs Deprecated type()

---

> **TL;DR:** `fill()` is like pasting text instantly into an input field after wiping it clean, while the deprecated `type()` method typed characters one keystroke at a time like an old mechanical typewriter.

---

## Why This Exists

Early versions of Playwright provided two methods for populating text fields: `type()` (simulating individual keystrokes) and `fill()` (setting element values directly). Over time, `type()` caused confusion because developers often forgot that it appended text to existing values rather than clearing input fields first. Playwright deprecated `type()` in favor of `fill()` for consistent, fast input handling.

---

## Mental Model

Imagine filling out a physical paper form that already has pencil writing in a box.
- **`type("new text")` [Deprecated]:** Takes a pencil and writes "new text" right after the existing pencil text without erasing it first.
- **`fill("new text")` [Standard]:** Takes an eraser, completely wipes the box clean, and writes "new text" clearly inside.

---

## How It Works

### `fill(value)` [Standard & Recommended]
1. Waits for actionability checks (element attached, visible, stable, enabled, editable).
2. Clears any existing text inside the target input/textarea field automatically.
3. Sets the element's `value` attribute directly.
4. Triggers input events (`input`, `change`, `blur`) to ensure framework state handlers (React, Angular, Vue) register the updated data.

### `type(text)` [Deprecated]
1. Focuses element and types characters sequentially with optional per-key delays (`{ delay: 100 }`).
2. Does **NOT** clear existing input values automatically!
3. Deprecated in recent Playwright releases—replaced by `fill()` for standard input and `pressSequentially()` for key-by-key simulation.

---

## Key Characteristics

- **Automatic Input Clearing:** `fill()` wipes existing field text before entering new data.
- **Fast Execution:** `fill()` sets input values instantly without keypress delays.
- **Frame State Triggering:** Emits standard browser DOM events so reactive frameworks detect value mutations.

---

## Common Mistakes

- **Using deprecated `type()` in modern Playwright code:** Seeing strikethrough syntax in IDE editors due to deprecation warnings.
- **Attempting `fill("")` to clear fields without knowing `clear()` exists:** While `await locator.fill('')` wipes input fields effectively, Playwright also provides `await locator.clear()`.

---

## Code Comparison Matrix

```javascript
// Demonstrating input action methods
const { test, expect } = require('@playwright/test');

test('input field population', async ({ page }) => {
  await page.goto('https://example.com/login');

  const usernameInput = page.locator('#username');

  // 1. Recommended Input Action (fill clears field first)
  await usernameInput.fill('first_attempt');
  await usernameInput.fill('corrected_username'); // Wipes 'first_attempt' and sets 'corrected_username'

  // 2. Clearing Fields Explicitly
  await usernameInput.clear(); // Wipes text completely

  // 3. Simulating Real Human Keypress Delays (Replaces deprecated type())
  // Use pressSequentially ONLY when testing autocomplete/search drop-downs that respond to keyup events!
  await usernameInput.pressSequentially('search_query', { delay: 100 });
});
```

---

## Key Takeaways

- Use `fill()` as the standard method to enter data into input fields.
- `fill()` automatically clears pre-existing text before inserting new values.
- Replace deprecated `type()` with `fill()` for standard inputs or `pressSequentially()` for real-time keypress event testing.

---

## Related

- [[pw-locators-css-selectors]] — Locating input elements
- [[pw-auto-waiting]] — Actionability checks executed before fill
- [[MOC - Playwright Locators and Actions]]
