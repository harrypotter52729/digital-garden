---
id: 202608152041
title: Dynamic REST API Data Pre-Conditioning for Web UI Tests
aliases:
  - api data setup
  - pre-condition API calls
  - fast test data creation
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/architecture
date_created: 2026-08-15
mastery_level: 1
---

# Dynamic REST API Data Pre-Conditioning for Web UI Tests

---

> **TL;DR:** Creating test data (like placing an order) via fast REST API calls in `test.beforeAll()` before executing UI assertions eliminates 80% of repetitive UI navigation steps and isolates UI testing strictly to the target component under test.

---

## Why This Exists

If a test spec's sole objective is to verify that a newly created order appears in the "Order History" table, executing manual UI steps (browsing products, adding to cart, filling shipping address, clicking checkout) for every test run is wasteful and prone to UI rendering flakiness. Playwright enables tests to trigger backend REST APIs directly (`apiContext.post('/api/create-order')`), generate real test data in milliseconds, and jump directly to UI verification.

---

## Mental Model

Imagine testing a hotel room keycard scanner.
- **Pure UI Approach:** Building the entire hotel from the foundation up, painting the door, installing the keycard lock, and manufacturing a keycard every single time you want to tap a card against the scanner.
- **API Pre-Conditioning Approach:** Calling the factory API to ship a pre-coded keycard directly to your hand (`createOrder API`). You walk up to the door and tap the keycard against the scanner immediately.

---

## How It Works

1. **Authentication:** Obtain a session JWT token via API or pre-saved storage state.
2. **Execute REST Creation Endpoint:** Send a POST request containing required payload parameters (e.g., `productId`, `country`) and Authorization headers:
   ```javascript
   const orderResponse = await apiContext.post('https://example.com/api/create-order', {
     data: { orders: [{ country: 'Cuba', productOrderedId: '65a83b' }] },
     headers: { Authorization: token, 'Content-Type': 'application/json' },
   });
   ```
3. **Parse Dynamic Response Data:** Extract generated dynamic IDs (`const orderId = responseJson.orders[0];`).
4. **Execute Target UI Test:** Navigate directly to the UI verification page (`page.goto('/orders')`) and assert the generated `orderId`.

---

## Key Characteristics

- **Massive Time Savings:** Drops prerequisite setup time from 10 seconds per spec down to 150 milliseconds.
- **Test Isolation:** Prevents UI bugs on checkout pages from causing unrelated false-negative failures on Order History specs.
- **Realistic Data Creation:** Generates real backend database records rather than mocking UI states.

---

## Common Mistakes

- **Using API pre-conditioning to replace end-to-end checkout UI tests:** Using APIs to skip UI checkout when your test's primary goal *is* to test checkout form validation! Only use API pre-conditioning for prerequisite data setup when testing downstream components.
- **Hardcoding authorization tokens:** Omitting dynamic authentication, causing API calls to return `401 Unauthorized`.

---

## Canonical Code Example

```javascript
// Dynamic REST API order creation pre-conditioning prior to UI validation
const { test, expect, request } = require('@playwright/test');

let token;
let createdOrderId;

test.beforeAll(async () => {
  const apiContext = await request.newContext();

  // 1. Authenticate via REST API
  const loginRes = await apiContext.post('https://example.com/api/auth/login', {
    data: { userEmail: 'user@test.com', userPassword: 'Password123' },
  });
  const loginJson = await loginRes.json();
  token = loginJson.token;

  // 2. Pre-condition test data: Create order via REST API
  const orderRes = await apiContext.post('https://example.com/api/create-order', {
    data: { orders: [{ country: 'Cuba', productOrderedId: '65a83b9e' }] },
    headers: { Authorization: token, 'Content-Type': 'application/json' },
  });
  const orderJson = await orderRes.json();
  createdOrderId = orderJson.orders[0];
  console.log(`Pre-conditioned Order ID via API: ${createdOrderId}`);
});

test('verify API-created order appears in Order History UI table', async ({ page }) => {
  // Inject session token and navigate straight to Order History UI
  await page.addInitScript((t) => window.localStorage.setItem('token', t), token);
  await page.goto('https://example.com/orders');

  // Assert API-created order ID appears in UI table
  const row = page.locator('tbody tr').filter({ hasText: createdOrderId });
  await expect(row).toBeVisible();
});
```

---

## Key Takeaways

- Trigger backend REST endpoints in `test.beforeAll()` to create test data fast.
- Extract dynamic IDs and pass them into downstream UI verification specs.
- Reserves UI navigation testing strictly for dedicated E2E flows.

---

## Related

- [[pw-hybrid-api-ui-auth-bypass-token-injection]] — Token injection setup
- [[pw-api-utils-class-abstraction]] — Modular API utils design
- [[MOC - Playwright Framework Design]]
