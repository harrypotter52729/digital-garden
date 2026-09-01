---
id: 202608152016
title: Checkbox and Radio Button Assertions with toBeChecked and isChecked
aliases:
  - toBeChecked
  - isChecked
  - checkbox assertions
  - radio assertions
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Checkbox and Radio Button Assertions with toBeChecked and isChecked

---

> **TL;DR:** `await expect(locator).toBeChecked()` is an automated quality inspector that retries until a checkbox or radio button is checked, while `locator.isChecked()` is a quick boolean status check (`true`/`false`) without retries.

---

## Why This Exists

Validating user agreement checkboxes, terms of service toggles, or radio button selections requires verifying element checked states. Playwright provides web-first assertions (`toBeChecked()`) that auto-retry until elements reach target state, alongside helper evaluation methods (`isChecked()`) for custom conditional logic.

---

## Mental Model

Imagine inspecting an electrical light switch.
- **`await expect(switch).toBeChecked()` (Web-First Assertion):** Stands at the wall and periodically checks the switch over 5 seconds. If a electrician turns the switch ON 1 second later, the test passes.
- **`await switch.isChecked()` (Boolean Evaluation):** Reads the current state of the switch right now at microsecond zero, returning `true` (ON) or `false` (OFF) without waiting.

---

## How It Works

### 1. Web-First Assertion: `await expect(locator).toBeChecked()`
- Auto-retries over the assertion timeout (default 5s).
- Passes if element is checked; fails and throws descriptive error if unchecked after timeout.
- Inverted check: `await expect(locator).not.toBeChecked()`.

### 2. Boolean Method: `await locator.isChecked()`
- Returns a boolean Promise (`Promise<boolean>`).
- Does **NOT** auto-retry! Returns current status instantly.
- Useful when writing conditional branch logic:  
  `if (!(await checkbox.isChecked())) { await checkbox.check(); }`

---

## Key Characteristics

- **Auto-Retrying Assertion:** `toBeChecked()` retries automatically, preventing race conditions.
- **Support for Inversion:** Pair with `.not` (`await expect(locator).not.toBeChecked()`) to assert unchecked states.
- **Boolean Logic Helper:** `isChecked()` provides raw boolean values for conditional script branching.

---

## Common Mistakes

- **Using `isChecked()` in test assertions without `expect()`:** Writing `await locator.isChecked()` on its own. It returns a boolean value but does **NOT** cause test failure if `false`! Always wrap assertions inside `expect()`.
- **Forgetting `.not` when asserting unchecked state:** Using custom boolean workarounds (`expect(await locator.isChecked()).toBe(false)`) instead of clean web-first syntax (`await expect(locator).not.toBeChecked()`).

---

## Code Comparison

### Incorrect (Manual Boolean Assertion — Flaky without Auto-Retry)

```javascript
// FLAKY: Checks boolean state once without retrying!
const isTermsChecked = await page.locator('#terms').isChecked();
expect(isTermsChecked).toBe(true);
```

### Correct (Web-First Assertion — Auto-Retrying & Clean)

```javascript
// RELIABLE: Retries over 5 seconds until element is checked
await expect(page.locator('#terms')).toBeChecked();

// Assertion for UNCHECKED state using .not
await expect(page.locator('#newsletter-opt-in')).not.toBeChecked();
```

---

## Canonical Code Example

```javascript
// Asserting checkbox and radio button states in Playwright
const { test, expect } = require('@playwright/test');

test('assert checkbox and radio button states', async ({ page }) => {
  await page.goto('https://example.com/form');

  const termsCheckbox = page.locator('#terms-checkbox');
  const newsletterOptIn = page.locator('#newsletter-checkbox');
  const userRoleRadio = page.locator('input[name="role"][value="user"]');

  // 1. Perform interactions
  await termsCheckbox.check();
  await userRoleRadio.check();

  // 2. Web-First Assertions for checked states (Auto-Retrying)
  await expect(termsCheckbox).toBeChecked();
  await expect(userRoleRadio).toBeChecked();

  // 3. Web-First Assertion for unchecked state
  await expect(newsletterOptIn).not.toBeChecked();

  // 4. Using boolean isChecked() inside conditional branching
  if (!(await newsletterOptIn.isChecked())) {
    console.log('Opt-in checkbox is currently unchecked as expected.');
  }
});
```

---

## Key Takeaways

- Use `await expect(locator).toBeChecked()` for auto-retrying assertions.
- Use `await expect(locator).not.toBeChecked()` for unchecked assertions.
- Reserve `await locator.isChecked()` for conditional branching logic inside scripts.

---

## Related

- [[pw-radio-buttons-web-popups-inspector]] — Interacting with radio buttons
- [[pw-test-assertions-expect]] — Web-first assertion engine
- [[MOC - Playwright Assertions]]
