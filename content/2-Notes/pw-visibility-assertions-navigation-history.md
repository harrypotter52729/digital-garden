---
id: 202608152037
title: Element Visibility Assertions and Browser History Navigation
aliases:
  - toBeVisible
  - toBeHidden
  - goBack goForward
  - browser history navigation
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/execution
date_created: 2026-08-15
mastery_level: 1
---

# Element Visibility Assertions and Browser History Navigation

---

> **TL;DR:** Web-first visibility assertions (`toBeVisible()`, `toBeHidden()`) automatically retry until elements transition state, while history methods (`page.goBack()`, `page.goForward()`) control browser back/forward navigation.

---

## Why This Exists

Testing dynamic UIs requires verifying that elements appear or disappear when toggled (e.g., clicking "Hide" hides a text box, clicking "Show" displays it). Traditional assertions fail if checked instantly before CSS transitions complete. Playwright provides web-first `toBeVisible()` and `toBeHidden()` assertions that auto-retry over a 5-second window, along with `goBack()` and `goForward()` for browser navigation.

---

## Mental Model

Imagine a stage magic trick.
- **Instant Non-Retrying Assertion:** Looking at the stage the exact millisecond the magician snaps their fingers. The curtain is still moving, so you incorrectly declare the trick failed.
- **Web-First Assertion (`toBeHidden()`):** Watching the stage continuously over 5 seconds (`auto-retry`). Once the curtain settles and the assistant disappears, you assert success.

---

## How It Works

### Visibility Assertions
- `await expect(locator).toBeVisible()`: Auto-retries until target element is attached to DOM and visible on screen.
- `await expect(locator).toBeHidden()`: Auto-retries until target element is detached from DOM or styled with `display: none` / `visibility: hidden`.

### Browser History Navigation
- `await page.goBack()`: Simulates clicking browser Back button.
- `await page.goForward()`: Simulates clicking browser Forward button.

---

## Key Characteristics

- **Auto-Retrying Assertions:** Retries visibility state checks automatically over default 5000ms timeout.
- **Native Browser History Control:** Navigates backward and forward through browser session history stack.
- **CSS Transition Resilience:** Accommodates fade-in/fade-out animation delays.

---

## Common Mistakes

- **Forgetting `await` on `goBack()` or `goForward()`:** Writing `page.goBack();` without `await`, causing subsequent script actions to run while navigation is still in flight.
- **Confusing `toBeHidden()` with manual `isVisible()` checks:** Writing `expect(await locator.isVisible()).toBe(false)`. Manual `isVisible()` checks evaluate instantly without waiting for animations; `toBeHidden()` retries automatically.

---

## Canonical Code Example

```javascript
// Demonstrating web-first visibility assertions and browser history navigation
const { test, expect } = require('@playwright/test');

test('verify element visibility toggle and browser history navigation', async ({ page }) => {
  await page.goto('https://example.com/practice');

  const textBox = page.locator('#displayed-text');
  const hideBtn = page.locator('#hide-textbox');
  const showBtn = page.locator('#show-textbox');

  // 1. Assert element is visible by default
  await expect(textBox).toBeVisible();

  // 2. Click Hide and assert element transitions to hidden
  await hideBtn.click();
  await expect(textBox).toBeHidden();

  // 3. Click Show and assert element becomes visible again
  await showBtn.click();
  await expect(textBox).toBeVisible();

  // 4. Test Browser History Navigation
  await page.goto('https://google.com');
  await page.goBack(); // Navigate back to practice page
  await expect(page.locator('#show-textbox')).toBeVisible();

  await page.goForward(); // Navigate forward to google.com
  await expect(page).toHaveURL(/google\.com/);
});
```

---

## Key Takeaways

- Use `toBeVisible()` and `toBeHidden()` for auto-retrying element visibility assertions.
- Use `page.goBack()` and `page.goForward()` for browser history stack navigation.
- Avoid instant boolean checks (`isVisible()`) when testing dynamic show/hide transitions.

---

## Related

- [[pw-visibility-checks-and-wait-operations]] — `isVisible()` vs `toBeVisible()`
- [[pw-test-assertions-expect]] — Web-first assertion engine
- [[MOC - Playwright Assertions]]
