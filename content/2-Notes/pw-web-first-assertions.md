---
id: 202606131231
title: Web-first Assertions
aliases:
  - playwright-assertions
  - async-assertions
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/assertions
date_created: 2026-06-13
mastery_level: 1
---

# Web-first Assertions

> **TL;DR:** Playwright's **Web-first Assertions** (`expect(locator).toBeVisible()`) are asynchronous checks that automatically poll the page until the target state is reached. Unlike static assertions that check a value once and fail if the page is mid-load, Web-first Assertions wait up to a timeout, completely eliminating timing-related test flakiness.

## The Photo Finish Analogy

Imagine trying to capture a photo of the winning horse crossing the finish line in a race:

```
Static Assertion:
[ Click shutter button exactly at 0.00s ] 
  --> Horse is not there yet! 
  --> Assertion FAILS! ❌

Web-first Assertion:
[ Start high-speed video camera ] 
  --> Keep checking frames: [No] -> [No] -> [Yes! Crossed at 1.45s] 
  --> Assertion PASSES!  (Within 5s timeout window)
```

- **Static/Flaky Assertions:** `expect(await page.isVisible('.success-banner')).toBe(true)`
  - *How it works:* You query the page *once* to get a boolean value, and evaluate it immediately. If the backend is 50 milliseconds slow, the banner hasn't appeared yet, returning `false`, and the test fails.
- **Web-first Assertions:** `await expect(page.locator('.success-banner')).toBeVisible()`
  - *How it works:* Playwright monitors the element, repeatedly evaluating its visibility (polling) up to a configuration timeout (default: 5 seconds). The moment it becomes visible, the assertion passes and execution continues.

---

## Polling Under the Hood

When you await a web-first assertion, Playwright continuously loops through these checks:

1. Locate the element in the DOM.
2. Evaluate the criteria (e.g., is it visible? does it contain text?).
3. If criteria is met: Resolve the Promise successfully.
4. If criteria is not met: Wait a few milliseconds and try again.
5. If the timeout (e.g., 5000ms) expires before the criteria is met: Reject the Promise and fail the test.

This makes tests highly resilient to page load speed fluctuations (such as running on resource-constrained CI servers).

---

## Static vs. Web-first Assertions Guide

| Test Criteria | Flaky Style (Static Checks) | Resilient Style (Web-First) |
| :--- | :--- | :--- |
| Element Visibility | `expect(await page.isVisible('#btn')).toBe(true)` | `await expect(page.locator('#btn')).toBeVisible()` |
| Input Text Value | `expect(await page.inputValue('#input')).toBe('John')` | `await expect(page.locator('#input')).toHaveValue('John')` |
| Element Enabled State | `expect(await page.isEnabled('#btn')).toBe(false)` | `await expect(page.locator('#btn')).toBeDisabled()` |
| Page URL Check | `expect(page.url()).toBe('https://domain/home')` | `await expect(page).toHaveURL(/.*home/)` |

---

## Canonical Code Example

This code demonstrates utilizing web-first assertions to handle asynchronous page state transitions securely:

```javascript
// @ts-check
const { test, expect } = require("@playwright/test");

test("Asynchronous form validation using Web-first assertions", async ({ page }) => {
  await page.goto("/dynamic-form");

  const submitBtn = page.getByRole("button", { name: "Submit" });
  const validationAlert = page.locator(".alert-validation");

  // 1. Assert initial page state (should be immediate since page loaded)
  await expect(submitBtn).toBeDisabled();

  // Fill in required fields
  await page.getByLabel("Email").fill("sdet_pro@test.com");
  await page.getByLabel("Password").fill("Secret123!");

  // 2. The submit button is enabled via JS after validation passes
  // Playwright polls the button state until it is enabled
  await expect(submitBtn).toBeEnabled();

  // Click submit to trigger an async network post
  await submitBtn.click();

  // 3. The alert message appears after the network promise resolves
  // Playwright automatically polls the alert's text contents
  await expect(validationAlert).toHaveText("Registration successful!");

  // 4. Assert URL matches expectations using regular expression matching
  await expect(page).toHaveURL(/\/dashboard/);
});
```

---

## Related
* [[pw-actionability-and-auto-waiting]] - How auto-waiting action checks differ from assertion polling states.
* [[js-async-await-syntax]] - How async/await syntax is structured to handle pending promise resolutions.
* [[js-promises-anatomy-and-chaining]] - Asynchronous assertions poll the page and resolve once the expected state is met.
* [[js-async-await-syntax]] - Web-first assertions must be awaited to block test execution until the criteria resolves.
