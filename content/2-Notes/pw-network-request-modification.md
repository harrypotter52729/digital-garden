---
id: 202608152048
title: Network Request Interception and Modification with route.continue
aliases:
  - route.continue
  - request modification
  - request tampering security test
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/network
date_created: 2026-08-15
mastery_level: 1
---

# Network Request Interception and Modification with route.continue

---

> **TL;DR:** `route.continue({ url, headers })` intercepts outgoing HTTP requests *before* they leave the browser and modifies request URLs or headers—allowing security automation tests to verify that backends reject unauthorized parameter tampering.

---

## Why This Exists

Security and authorization testing requires verifying that backend API servers enforce strict authorization checks (e.g., verifying user B cannot view user A's order details by swapping order IDs in request URLs). Playwright provides `route.continue()`, enabling tests to intercept outgoing HTTP requests, modify request parameters/headers, and forward the modified request to real backend servers.

---

## Mental Model

Imagine a mischievous mailroom prankster.
- **Normal Request:** An employee drops an envelope into the mail slot addressed to "My Account Details" (`orderId = 100`).
- **Request Modification (`route.continue()`):** The prankster (`page.route()`) intercepts the envelope before it leaves the building, erases "My Account Details", writes "Executive Financial Vault" (`orderId = 999`), and sends the tampered envelope to the bank. The bank rejects the request (`403 Forbidden`), proving security controls work.

---

## How It Works

1. **Register Request Interception:**
   ```javascript
   await page.route('**/api/v1/order-details/*', async (route) => {
     // Tamper with target URL ID (Change user's valid ID to unauthorized ID)
     const tamperedUrl = 'https://example.com/api/v1/order-details/UNAUTHORIZED_ID_999';
     
     // Forward modified request to server
     await route.continue({ url: tamperedUrl });
   });
   ```
2. **Trigger Action:** Click the UI button that dispatches the request.
3. **Verify Server Security Handling:** Assert that the UI renders a `"403 Forbidden"` or `"You are not authorized"` security error.

---

## Key Characteristics

- **Real Backend Forwarding:** Unlike `route.fulfill()` (which returns static mocks), `route.continue()` sends the tampered request to real backend servers.
- **Header & Parameter Tampering:** Modify headers (`Authorization`, `Cookie`), request URLs, or HTTP methods (`GET` $\rightarrow$ `DELETE`).
- **Automated Security Verification:** Test Broken Object Level Authorization (BOLA / IDOR) vulnerabilities automatically.

---

## Common Mistakes

- **Confusing `route.continue()` with `route.fulfill()`:** `route.fulfill()` stops the request and returns a mock response locally; `route.continue()` forwards the modified request outward to the server.
- **Forgetting to format modified options as objects:** Passing string arguments directly to `route.continue('https://...')`. `route.continue()` expects an options object `{ url: 'https://...' }`.

---

## Canonical Code Example

```javascript
// Intercepting outgoing HTTP request and tampering with URL parameter (Security BOLA Test)
const { test, expect } = require('@playwright/test');

test('verify backend blocks unauthorized order ID request tampering', async ({ page }) => {
  await page.goto('https://example.com/orders');

  // 1. Intercept outgoing order details API request
  await page.route('**/api/v1/get-order-details*', async (route) => {
    // Modify target order ID in request URL to attempt unauthorized access
    const tamperedUrl = 'https://example.com/api/v1/get-order-details?id=unauthorized_99999';

    // Forward tampered request to backend server
    await route.continue({ url: tamperedUrl });
  });

  // 2. Click 'View Order' button for user's legitimate order
  await page.locator('button:has-text("View")').first().click();

  // 3. Assert backend server rejects tampered request with security error
  const errorBanner = page.locator('.error-banner');
  await expect(errorBanner).toBeVisible();
  await expect(errorBanner).toHaveText(/You are not authorized/i);
});
```

---

## Key Takeaways

- Use `route.continue({ url, headers })` to modify outgoing HTTP requests before forwarding them to servers.
- Ideal for automating security, BOLA/IDOR, and authorization testing.
- Forwarded requests hit real backend API endpoints.

---

## Related

- [[pw-network-response-interception-mocking]] — Mocking responses with `route.fulfill`
- [[pw-network-request-aborting-telemetry]] — Aborting network requests
- [[MOC - Playwright Network Interception]]
