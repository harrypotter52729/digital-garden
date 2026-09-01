---
id: 202608152046
title: Network Response Interception and Mocking with page.route and route.fulfill
aliases:
  - page.route
  - route.fulfill
  - mock response
  - response interception
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/network
date_created: 2026-08-15
mastery_level: 1
---

# Network Response Interception and Mocking with page.route and route.fulfill

---

> **TL;DR:** `page.route('**/api/endpoint', route => route.fulfill({ responsePayload }))` intercepts outgoing HTTP requests matching URL patterns and returns custom mock JSON responses before they reach the browser UI—allowing you to test edge cases without modifying backend databases.

---

## Why This Exists

Testing UI error states (like an empty "No Orders Found" screen, 500 Server Error messages, or empty cart states) often requires clearing production databases or waiting for rare backend failures. Playwright provides **Network Interception** (`page.route()`), enabling tests to intercept HTTP requests and substitute custom mock JSON responses on the fly.

---

## Mental Model

Imagine a post office worker sorting mail.
- **Normal Network Request:** A letter addressed to "Customer Orders" is delivered straight to the house. The house displays whatever is inside the real letter.
- **Interception with `route.fulfill()`:** The post worker (`page.route()`) intercepts the letter mid-transit, removes the real contents, slips a custom note inside reading "Zero Orders Found" (`route.fulfill()`), and hands the modified letter to the house. The house displays the empty state message.

---

## How It Works

1. **Register Route Handler BEFORE Triggering Action:**  
   Define URL pattern matchers and fulfillment payloads before triggering page actions:
   ```javascript
   await page.route('**/api/get-orders*', async (route) => {
     const fakeResponseBody = { data: [], message: 'No orders' };
     await route.fulfill({
       status: 200,
       contentType: 'application/json',
       body: JSON.stringify(fakeResponseBody),
     });
   });
   ```
2. **Trigger UI Action:** Click the button or navigate to the page that dispatches the target REST call (`await page.click('#my-orders-btn')`).
3. **Mock Rendering:** The browser receives the mocked JSON payload and renders the empty/error state UI.

---

## Key Characteristics

- **Zero Database Mutation:** Simulates empty, corrupted, or error states without altering real backend database records.
- **Wildcard URL Matching:** Supports glob patterns (`**/api/orders/*`) and regular expressions.
- **Instant Response Overrides:** Replaces HTTP response bodies, status codes (`403`, `500`), and headers.

---

## Common Mistakes

- **Registering `page.route()` AFTER triggering the action:** Calling `page.click()` before `page.route()`. The real network request fires before the handler attaches!
- **Passing a JavaScript object directly to `body`:** Passing an un-stringified JavaScript object `{ data: [] }` to `body`. `route.fulfill()` requires strings; use `JSON.stringify(object)` or `json: object`.

---

## Canonical Code Example

```javascript
// Mocking REST API response payload to test empty UI state
const { test, expect } = require('@playwright/test');

test('mock empty orders API response and verify UI empty state message', async ({ page }) => {
  await page.goto('https://example.com/login');

  // 1. Intercept network API call BEFORE clicking orders link
  await page.route('**/api/v1/customer-orders/*', async (route) => {
    // Fulfill request with mock empty data JSON response
    await route.fulfill({
      status: 200,
      contentType: 'application/json',
      body: JSON.stringify({ data: [], message: 'No orders found' }),
    });
  });

  // 2. Perform UI action that triggers the intercepted API call
  await page.click('button:has-text("My Orders")');

  // 3. Assert UI renders empty state message based on mock payload
  const emptyMessageLocator = page.locator('.empty-orders-banner');
  await expect(emptyMessageLocator).toBeVisible();
  await expect(emptyMessageLocator).toHaveText('You have no orders to show at this time.');
});
```

---

## Key Takeaways

- Register `page.route()` *before* performing UI actions that trigger the network call.
- Use `route.fulfill({ body: JSON.stringify(fakePayload) })` to override HTTP responses.
- Enables deterministic testing of edge cases (empty states, 500 errors, rate limits).

---

## Related

- [[pw-wait-for-response-synchronization]] — Synchronizing response waits
- [[pw-network-request-modification]] — Intercepting and modifying requests
- [[MOC - Playwright Network Interception]]
