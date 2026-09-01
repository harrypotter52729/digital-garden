---
id: 202608152042
title: Modular API Utility Abstraction Layer
aliases:
  - APIUtils
  - API abstraction layer
  - modular api helper
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/architecture
date_created: 2026-08-15
mastery_level: 1
---

# Modular API Utility Abstraction Layer

---

> **TL;DR:** Encapsulating REST API calls inside a dedicated `APIUtils` helper class decouples API request logic, headers, and authentication tokens from test specs—providing a single reusable interface for data setup.

---

## Why This Exists

Writing raw `request.post()` calls, parsing JSON strings, and setting `Authorization` headers inside spec files creates code duplication across test suites. If an API endpoint structure or header format changes, developers must update dozens of spec files. An `APIUtils` class encapsulates REST request construction into clean, high-level methods (`apiUtils.createOrder(payload)`).

---

## Mental Model

Imagine an executive ordering lunch.
- **Un-abstracted Approach:** The executive calls the farm directly, orders raw flour, milk, and eggs, hires a baker, bakes a pizza, and washes the dishes in their office.
- **`APIUtils` Abstraction Layer:** The executive dials a single concierge number (`apiUtils.createOrder('Pizza')`). The concierge handles backend logistics, food prep, and payment, delivering a hot pizza directly to the executive's desk.

---

## How It Works

1. **Constructor Injection:** The `APIUtils` class receives the `apiContext` and `loginPayload` in its constructor, binding them to instance variables (`this.apiContext`, `this.loginPayload`).
2. **Encapsulated Helper Methods:**
   - `getToken()`: Executes POST request to `/api/auth/login` and returns the JWT token.
   - `createOrder(orderPayload)`: Calls `getToken()`, attaches the token to `Authorization` headers, posts the `orderPayload`, and returns a clean response object (`{ token, orderId }`).
3. **Module Export:** Export the class using `module.exports = { APIUtils };`.

---

## Class Architecture

```text
┌────────────────────────────────────────────────────────┐
│                   class APIUtils                       │
├────────────────────────────────────────────────────────┤
│ - apiContext: APIRequestContext                        │
│ - loginPayload: Object                                 │
├────────────────────────────────────────────────────────┤
│ + constructor(apiContext, loginPayload)                │
│ + getToken(): Promise<string>                          │
│ + createOrder(orderPayload): Promise<{token, orderId}> │
└────────────────────────────────────────────────────────┘
```

---

## Common Mistakes

- **Omitting `await` on internal `APIUtils` methods:** Calling `this.getToken()` inside `createOrder()` without `await`, causing authorization headers to send a pending Promise string.
- **Forgetting `module.exports`:** Omitting `module.exports = { APIUtils }`, making the class inaccessible to external test specs.

---

## Canonical Code Example

### `utils/APIUtils.js`

```javascript
// Encapsulating API request logic into a modular utility class
class APIUtils {
  constructor(apiContext, loginPayload) {
    this.apiContext = apiContext;
    this.loginPayload = loginPayload;
  }

  async getToken() {
    const loginResponse = await this.apiContext.post('https://example.com/api/auth/login', {
      data: this.loginPayload,
    });
    const loginJson = await loginResponse.json();
    return loginJson.token;
  }

  async createOrder(orderPayload) {
    // Acquire authentication token
    const token = await this.getToken();

    // Execute order creation API request
    const orderResponse = await this.apiContext.post('https://example.com/api/create-order', {
      data: orderPayload,
      headers: {
        Authorization: token,
        'Content-Type': 'application/json',
      },
    });

    const orderJson = await orderResponse.json();
    const orderId = orderJson.orders[0];

    // Return structured object containing token and generated order ID
    return { token, orderId };
  }
}

module.exports = { APIUtils };
```

### Spec Usage: `tests/orderVerification.spec.js`

```javascript
const { test, expect, request } = require('@playwright/test');
const { APIUtils } = require('../utils/APIUtils');

let apiData;

test.beforeAll(async () => {
  const apiContext = await request.newContext();
  const loginPayload = { userEmail: 'user@test.com', userPassword: 'Password123' };
  const orderPayload = { orders: [{ country: 'Cuba', productOrderedId: '65a83b' }] };

  // Instantiate helper and execute order creation in one line
  const apiUtils = new APIUtils(apiContext, loginPayload);
  apiData = await apiUtils.createOrder(orderPayload);
});

test('verify order in UI using APIUtils data', async ({ page }) => {
  await page.addInitScript((t) => window.localStorage.setItem('token', t), apiData.token);
  await page.goto('https://example.com/orders');

  await expect(page.locator('tbody tr').filter({ hasText: apiData.orderId })).toBeVisible();
});
```

---

## Key Takeaways

- Encapsulate REST API requests inside modular `APIUtils` helper classes.
- Use constructor injection to manage API contexts and login payloads.
- Keeps test spec files clean, readable, and focused on assertions.

---

## Related

- [[pw-api-data-preconditioning-order-creation]] — Pre-conditioning test data
- [[pw-hybrid-api-ui-auth-bypass-token-injection]] — Token injection
- [[MOC - Playwright Framework Design]]
