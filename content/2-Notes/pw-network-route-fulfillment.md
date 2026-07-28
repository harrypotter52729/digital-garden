---
id: 202607282000
title: Network Mocking & Route Fulfillments in Playwright
aliases:
  - network mocking playwright
  - page.route playwright
  - mocking api requests
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/playwright-network
date_created: {today}
mastery_level: 1
---

# Network Mocking & Route Fulfillments in Playwright

> **TL;DR:** Playwright's `page.route()` acts as an in-browser programmable proxy firewall—intercepting outbound HTTP requests and allowing tests to immediately return fake JSON payloads, mock 500 server errors, or inspect request headers without hitting backend servers!

---

## Canonical Code Example

```javascript
/**
 * Demonstrates intercepting API endpoints and fulfilling custom JSON responses in Playwright.
 */

import { test, expect } from '@playwright/test';

test('Mock product search API response', async ({ page }) => {
  // 1. Intercept network request to API endpoint
  await page.route('**/api/v1/products/search*', async (route) => {
    // Fulfill with mock JSON payload
    await route.fulfill({
      status: 200,
      contentType: 'application/json',
      json: [
        { id: 99, name: 'Mocked Super Laptop', price: 999.99 }
      ]
    });
  });

  // 2. Navigate to product page triggering search
  await page.goto('https://automationexercise.com/products');
  await page.getByPlaceholder('Search Product').fill('Laptop');
  await page.getByRole('button', { name: 'submit_search' }).click();

  // 3. Verify UI renders mocked payload
  await expect(page.getByText('Mocked Super Laptop')).toBeVisible();
});
```

---

## Related
- [[pw-api-interception-and-mocking]] — Intercepting API traffic.
- [[pw-browser-context-and-page-model]] — Browser contexts.
- [[MOC - Playwright Architecture]] — Playwright Architecture MOC.
