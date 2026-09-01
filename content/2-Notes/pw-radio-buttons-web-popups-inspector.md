---
id: 202608152015
title: Radio Buttons, Web Popups, and Debugging with Playwright Inspector
aliases:
  - radio buttons
  - web popups
  - playwright inspector
  - page.pause()
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/debugging
date_created: 2026-08-15
mastery_level: 1
---

# Radio Buttons, Web Popups, and Debugging with Playwright Inspector

---

> **TL;DR:** `page.pause()` opens the Playwright Inspector debugging window—acting as an interactive pause button that lets you step through test lines, inspect selector targets live, and test element interactions visually.

---

## Why This Exists

Automating form controls (radio buttons, HTML modal popups) often triggers dynamic layout changes that are hard to inspect when tests run at full speed. Playwright provides dedicated locator interactions (`check()`, `click()`) alongside `page.pause()` to launch Playwright Inspector for interactive step-by-step test debugging.

---

## Mental Model

Imagine debugging a live TV show.
- **Normal Execution:** The show plays at 1x speed and finishes in 2 seconds. You can't see why a specific stage prop fell over.
- **`await page.pause()`:** Pauses live broadcasting, turns on studio lights, and hands you an interactive remote control (`Playwright Inspector`). You can click "Step Over" to advance one scene at a time or hover over props to see their selector coordinates.

---

## How It Works

1. **Radio Button Interaction:**
   - Call `await locator.check()` or `await locator.click()` on target `<input type="radio">` elements.
2. **Web Modal Popups:**
   - Web-based HTML modal overlays (unlike browser-native JavaScript `alert()` dialogs) contain standard DOM locators. Locate the modal's confirm button (e.g., `#okayBtn`) and execute `.click()`.
3. **Playwright Inspector (`page.pause()`):**
   - Inserting `await page.pause()` inside a test pauses execution and opens the GUI Inspector.
   - Allows stepping line-by-line, editing CSS/XPath selectors live, and highlighting matching elements in the browser viewport.

---

## Key Characteristics

- **Interactive Debugger:** Playwright Inspector provides step-over, resume, and live selector recording tools.
- **`check()` vs `click()`:** `check()` checks radio buttons or checkboxes safely without toggling an already-checked element off.
- **Web Modal Locators:** HTML modals are inspected and targeted using standard CSS selectors.

---

## Common Mistakes

- **Leaving `await page.pause()` in committed code:** Committing `page.pause()` into CI/CD pipelines will cause continuous integration builds to hang indefinitely waiting for manual GUI interaction!
- **Confusing Web Modals with Native JS Dialogs:** Attempting `page.on('dialog')` for HTML `<div>` modals instead of using standard locator `.click()` calls.

---

## Canonical Code Example

```javascript
// Interacting with radio buttons, HTML popups, and using page.pause()
const { test, expect } = require('@playwright/test');

test('handle radio buttons, HTML popups, and debug with inspector', async ({ page }) => {
  await page.goto('https://example.com/form-demo');

  // 1. Select radio button using check()
  const userRadio = page.locator('input[type="radio"][value="user"]');
  await userRadio.check();

  // 2. Handle web-based HTML confirmation modal popup
  const modalConfirmBtn = page.locator('#okay-btn');
  await modalConfirmBtn.click(); // Standard DOM locator click

  // 3. Pause execution to debug interactively via Playwright Inspector
  // REMOVE before committing code!
  // await page.pause();

  // 4. Assert radio button is checked
  await expect(userRadio).toBeChecked();
});
```

---

## Key Takeaways

- Use `.check()` or `.click()` to select radio buttons safely.
- Web modal popups are part of the standard DOM—target them using standard locators.
- `await page.pause()` launches Playwright Inspector for step-by-step visual debugging.

---

## Related

- [[pw-checkbox-radio-assertions]] — Asserting checked states
- [[pw-select-dropdown-handling]] — Static dropdowns
- [[MOC - Playwright Execution]]
