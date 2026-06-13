---
id: 202606131227
title: Actionability & Auto-waiting
aliases:
  - playwright-auto-wait
  - actionability-checks
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/locating-interacting
date_created: 2026-06-13
mastery_level: 1
---

# Actionability & Auto-waiting

> **TL;DR:** Playwright automatically performs a battery of **actionability checks** (like verifying an element is visible, enabled, and has stopped moving) *before* performing any action like clicking or typing. This removes the need for brittle, hardcoded sleep commands (`sleep(3000)`), making your tests faster and highly reliable.

## The Polite Waiter Analogy

Imagine walking into a busy restaurant. A bad automation tool is like an impatient waiter shouting: *"WHAT DO YOU WANT TO EAT?!"* the millisecond you step through the front door, resulting in a crash.

Playwright is like a **polite, professional waiter** who does not approach your table until he checks that:
1. You are actually seated (**Attached** to the DOM).
2. You have a table and are not hidden behind a curtain (**Visible**).
3. You have stopped moving your chair around (**Stable** - no transitions/animations).
4. You are awake and not currently asleep (**Enabled**).
5. You are holding the menu (**Clickable/Receives Events**).

Only when all these conditions are met does Playwright take your order (execute the click/type action).

---

## Actionability Check Lifecycle

Before Playwright executes an action (e.g., `page.click('button')`), it checks the element against this strict lifecycle:

```
[ Element Selector ]
        |
        v
  1. Attached   -----> Is it present in the DOM tree?
        |
        v
  2. Visible    -----> Does it have a non-zero size and is it not hidden via CSS?
        |
        v
  3. Stable     -----> Has it stopped moving? (CSS transitions/animations complete)
        |
        v
  4. Enabled    -----> Is it not disabled via the 'disabled' attribute?
        |
        v
  5. Editable   -----> (For fill/type actions) Is it not read-only?
        |
        v
  6. Clickable  -----> Does it receive mouse pointer events? (Not covered by other layers)
        |
        v
[ Execute Action ]
```

If these checks are not met immediately, Playwright will automatically **wait** (up to the `actionTimeout` set in your configuration, e.g., 10 seconds) for the element to pass. If it passes within the window, the action executes smoothly. If it times out, the test fails with a clear log explaining which check failed.

---

## Anti-Pattern: Hardcoded Sleep Calls
Using hardcoded pauses (e.g., `await page.waitForTimeout(3000)`) is the single biggest cause of slow, flaky automation. 
* **If the application is fast:** You waste 3 seconds doing nothing.
* **If the application is slow:** (e.g., network slowdown on CI/CD), 3 seconds might not be enough, and the test fails anyway.

Always rely on **auto-waiting** or **explicit state changes** instead.

---

## Canonical Code Example

This example demonstrates utilizing auto-waiting, handling slow-loading pages, and specifying custom waiting conditions:

```javascript
// @ts-check
const { test, expect } = require("@playwright/test");

test("Handling dynamic elements and custom wait conditions", async ({ page }) => {
  await page.goto("/dynamic-controls");

  const enableButton = page.getByRole("button", { name: "Enable" });
  const inputField = page.getByRole("textbox");

  // Verify input is disabled initially
  await expect(inputField).toBeDisabled();

  // Clicking 'Enable' starts an asynchronous network/timer request
  await enableButton.click();

  // Playwright automatically WAITS for the input to pass actionability checks
  // (attached, visible, stable, enabled) before filling text!
  // No sleep() required.
  await inputField.fill("Automation is smooth!");

  // --- Handling Custom Wait Scenarios ---

  // Scenario A: Waiting for an element to disappear (e.g., loading spinner)
  const loadingSpinner = page.locator("#loading-spinner");
  await expect(loadingSpinner).toBeHidden({ timeout: 15000 }); // Custom wait up to 15s

  // Scenario B: Waiting for custom API / network responses before moving on
  // This ensures the test does not proceed until the backend confirms receipt
  const [response] = await Promise.all([
    page.waitForResponse(response => 
      response.url().includes("/api/save-profile") && response.status() === 200
    ),
    page.getByRole("button", { name: "Save Profile" }).click()
  ]);

  console.log(`Profile saved successfully. Status: ${response.status()}`);
});
```

---

## Related
* [[pw-built-in-locators-vs-css-xpath]] - How to find elements that Playwright can monitor for actionability.
* [[js-async-await-syntax]] - The structural mechanism behind pausing test threads until actions or assertions complete.
* [[pw-playwright-config-file]] - Setting default action timeouts globally.
* [[js-promises-anatomy-and-chaining]] - Playwright's auto-waiting loops rely on resolving JavaScript promises under the hood.
* [[js-async-await-syntax]] - Allows pausing test execution until dynamic elements resolve actionability checks.
* [[js-the-event-loop-and-call-stack]] - Actionability checks are queued and scheduled on the JavaScript event loop.
