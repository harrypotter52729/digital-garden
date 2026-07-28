---
id: 202607282001
title: Authentication Storage State Reuse in Playwright
aliases:
  - storageState playwright
  - auth reuse playwright
  - playwright global setup auth
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/playwright-auth
date_created: {today}
mastery_level: 1
---

# Authentication Storage State Reuse in Playwright

> **TL;DR:** `storageState` is Playwright's session saver—logging into an application once during setup, saving the session cookies and local storage tokens to a JSON file, and reusing them across hundreds of test specs to skip repeated UI login forms!

---

## Canonical Code Example

```javascript
/**
 * Demonstrates saving and reusing authentication storageState in Playwright.
 */

// 1. auth.setup.js (Setup spec)
import { test as setup, expect } from '@playwright/test';

const authFile = 'playwright/.auth/user.json';

setup('authenticate user', async ({ page }) => {
  await page.goto('https://automationexercise.com/login');
  await page.getByTestId('login-email').fill('user@example.com');
  await page.getByTestId('login-password').fill('password123');
  await page.getByRole('button', { name: 'Login' }).click();
  
  await page.waitForURL('**/account');

  // Save session state to JSON file
  await page.context().storageState({ path: authFile });
});

// 2. playwright.config.js snippet
// use: { storageState: 'playwright/.auth/user.json' }
```

---

## Related
- [[pw-authentication-state-reuse]] — Preserving auth states across tests.
- [[pw-playwright-config-file]] — Playwright global configuration options.
- [[MOC - Playwright Framework Design]] — Playwright Framework MOC.
