---
id: 202607282012
title: Combining APIRequestContext with UI Workflows
aliases:
  - APIRequestContext playwright
  - hybrid UI API testing
  - request fixture playwright
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/playwright-api
date_created: 2026-07-28
mastery_level: 1
---

# Combining `APIRequestContext` with UI Workflows

> **TL;DR:** Hybrid testing uses `request` to rapidly seed database records or authenticate via API in milliseconds, then uses `page` to verify the UI rendering—giving you full E2E validation at 10x the speed of pure UI test flows!

---

## Canonical Code Example

```javascript
/**
 * Hybrid test: Creates user via API request context, then logs in via UI.
 */
import { test, expect } from '@playwright/test';

test('Hybrid API + UI Test Flow', async ({ request, page }) => {
  const email = `user_${Date.now()}@test.com`;

  // 1. Seed user via REST API (Fast!)
  const response = await request.post('https://automationexercise.com/api/createAccount', {
    form: {
      name: 'Tester',
      email: email,
      password: 'password123'
    }
  });
  expect(response.status()).toBe(200);

  // 2. Perform UI login verification
  await page.goto('https://automationexercise.com/login');
  await page.getByPlaceholder('Email Address').first().fill(email);
  await page.getByPlaceholder('Password').fill('password123');
  await page.getByRole('button', { name: 'Login' }).click();

  // 3. Assert logged-in UI state
  await expect(page.getByText('Logged in as Tester')).toBeVisible();
});
```

---

## Related
- [[pw-api-interception-and-mocking]] — Network interception.
- [[pw-storage-state-auth]] — Reusing state across tests.
- [[MOC - Playwright Architecture]] — Playwright Architecture MOC.
