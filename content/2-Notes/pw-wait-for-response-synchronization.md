---
id: 202608152047
title: Async Event Synchronization with page.waitForResponse
aliases:
  - waitForResponse
  - response synchronization
  - network race condition
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/network
date_created: 2026-08-15
mastery_level: 1
---

# Async Event Synchronization with page.waitForResponse

---

> **TL;DR:** `page.waitForResponse('**/api/endpoint')` pauses execution until a specific network response completes, preventing race condition errors like `network request context disposed` during fast network mocking.

---

## Why This Exists

When mocking network calls via `page.route()`, execution speed can cause race conditions. If the test runner attempts to evaluate mock UI state before the browser finishes processing the intercepted HTTP response, Playwright throws `Error: apiRequestContext.fetch: Target page, context or browser has been closed` or `network context disposed`. `page.waitForResponse()` synchronizes test execution explicitly with network completion.

---

## Mental Model

Imagine waiting for a pizza delivery before sitting down at the dinner table.
- **Unsynchronized Execution:** Sitting down at the table and picking up your fork before the delivery driver has even driven down your street. You end up staring at an empty plate (`context disposed` error).
- **Synchronized `waitForResponse()`:** Sitting at the window and waiting until the delivery driver hands you the pizza box (`page.waitForResponse()`). Only after the pizza box is in your hands do you open it and start eating (`assertions`).

---

## How It Works

1. **Invocation:** Call `await page.waitForResponse(urlPattern)` concurrently or immediately after triggering UI actions.
2. **Predicate Matcher:** Accepts URL glob strings (`**/api/v1/orders*`) or predicate functions:
   ```javascript
   await page.waitForResponse(
     (response) =>
       response.url().includes('/api/v1/orders') && response.status() === 200,
   );
   ```
3. **Execution Pause:** Pauses test runner progression until the target HTTP response completes loading in the browser context.

---

## Key Characteristics

- **Race Condition Prevention:** Eliminates timing errors between network responses and DOM rendering.
- **Wildcard & Regex Support:** Supports glob patterns (`**/orders/*`) and regular expressions.
- **Predicate Filtering:** Filter responses by status code (`response.status() === 200`) or response headers.

---

## Common Mistakes

- **Awaiting `waitForResponse()` AFTER the network request has already finished:** Calling `waitForResponse()` lines after the UI action completed. If the network call finishes quickly, `waitForResponse()` hangs indefinitely!
- **Using `Promise.all()` to pair trigger and wait:** Recommended pattern:
  ```javascript
  await Promise.all([
    page.waitForResponse('**/api/v1/orders*'),
    page.click('#fetch-orders-btn'),
  ]);
  ```

---

## Canonical Code Example

```javascript
// Synchronizing network response completion using Promise.all and waitForResponse
const { test, expect } = require('@playwright/test');

test('synchronize network route fulfillment with waitForResponse', async ({ page }) => {
  await page.goto('https://example.com/dashboard');

  // 1. Setup network mock route
  await page.route('**/api/v1/orders/*', async (route) => {
    await route.fulfill({
      status: 200,
      contentType: 'application/json',
      body: JSON.stringify({ data: [] }),
    });
  });

  // 2. Synchronize UI click with network response completion using Promise.all
  await Promise.all([
    page.waitForResponse('**/api/v1/orders/*'), // Wait for response
    page.click('button:has-text("Refresh Orders")'), // Trigger network request
  ]);

  // 3. Perform assertions safely AFTER response completes
  await expect(page.locator('.orders-count')).toHaveText('0');
});
```

---

## Key Takeaways

- Use `page.waitForResponse()` to synchronize test execution with network completion.
- Combine with `Promise.all([ page.waitForResponse(), page.click() ])` to avoid race conditions.
- Resolves network context disposal and flakiness during API mocking.

---

## Related

- [[pw-network-response-interception-mocking]] — Network response mocking
- [[pw-network-request-modification]] — Network request interception
- [[MOC - Playwright Network Interception]]
