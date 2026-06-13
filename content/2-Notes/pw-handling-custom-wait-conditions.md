---
id: 202606131245
title: Handling Custom Wait Conditions
aliases:
  - playwright-wait-for-function
  - custom-polling
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/locating-interacting
date_created: 2026-06-13
mastery_level: 1
---

# Handling Custom Wait Conditions

> **TL;DR:** While Playwright automatically waits for elements to be clickable, some tests require waiting for non-visual criteria—such as a custom JavaScript library loading on the `window` object or an API array reaching a specific length. You can wait for these custom states using `page.waitForFunction()`.

## The Signal Flare Analogy

Imagine standing on a hill waiting for a cargo ship to load. 

```
Impattient/Flaky Strategy (Manual Sleep):
[ Sleep 5 seconds ] -> "The ship should be loaded by now, let's go." 
(If the crew ran into delays, the ship is empty and your check fails).

Active Polling (Default Wait):
[ Watch the gangway ] -> "I'll wait until the doors close."
(Standard element actionability checks).

Custom Waiting (waitForFunction):
[ Watch the mast ] -> "I'll wait here until the crew shoots a green signal flare."
(Monitoring window object parameters or array data lengths inside browser memory).
```

Playwright's `page.waitForFunction(fn)` acts as a watchman. It periodically executes your custom checking script inside the browser console, resolving the execution promise the exact millisecond the function returns a truthy value.

---

## Polling Settings

By default, `waitForFunction` evaluates your query on every animation frame (`requestAnimationFrame`). This makes checks incredibly fast and efficient. You can configure custom polling intervals if you want to poll less frequently to save CPU resources:
- **`polling: 'raf'`** (Default): Polls on browser repaint intervals (approx. every 16ms).
- **`polling: 100`**: Evaluates the function every 100 milliseconds.

---

## Canonical Code Example

This code illustrates waiting for a client-side library to initialize on the global window scope and polling a list container until its child elements reach a specific count.

```javascript
// @ts-check
const { test, expect } = require("@playwright/test");

test("Waiting for custom window state and DOM criteria", async ({ page }) => {
  await page.goto("/payment-checkout");

  // --- Scenario A: Wait for a global window object to initialize ---
  
  // We want to wait until window.stripeLoaded is true before interacting
  // This prevents tests from clicking payments before libraries finish parsing.
  await page.waitForFunction(() => {
    // Runs inside browser console scope
    return window["stripeLoaded"] === true;
  }, {
    timeout: 10000, // Timeout after 10s if stripe never loads
    polling: 100    // Check every 100ms
  });

  console.log("Stripe payment library initialized successfully!");

  // --- Scenario B: Wait for complex element counts ---
  
  const addBtn = page.getByRole("button", { name: "Add Mock Item" });
  await addBtn.click();
  await addBtn.click();

  // Wait until the dashboard contains exactly 2 active notifications
  // This passes the locator ID into the evaluation script securely.
  const notificationSelector = ".notification-item";
  
  await page.waitForFunction((selector) => {
    // Selects elements inside the browser page context
    const elements = document.querySelectorAll(selector);
    return elements.length === 2;
  }, notificationSelector);

  // Hard assertion to verify state is stable
  await expect(page.locator(notificationSelector)).toHaveCount(2);
});
```

---

## Related
* [[pw-actionability-and-auto-waiting]] - Comparing standard UI actionability loops with memory-state polling.
* [[js-promises-anatomy-and-chaining]] - How JS schedules promise tasks until conditional statements return truthy.
* [[pw-handling-js-evaluations]] - The serialization boundary rules governing how data passes into evaluation functions.
