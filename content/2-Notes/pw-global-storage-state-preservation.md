---
id: 202608152043
title: Global Authentication Storage State Preservation
aliases:
  - storageState
  - state.json
  - session storage preservation
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/architecture
date_created: 2026-08-15
mastery_level: 1
---

# Global Authentication Storage State Preservation

---

> **TL;DR:** `context.storageState({ path: 'state.json' })` dumps authenticated cookies and local storage tokens into a JSON file, allowing subsequent browser contexts to load authenticated states (`browser.newContext({ storageState: 'state.json' })`) instantly without logging in again.

---

## Why This Exists

When running test suites with 50+ spec files, authenticating via UI or API inside every single spec creates redundant execution overhead. Playwright provides native **Storage State Preservation** (`storageState`). You perform UI or API authentication once inside a global `beforeAll` hook or setup project, save the authenticated state snapshot to disk (`state.json`), and configure test projects to reuse `state.json` globally.

---

## Mental Model

Imagine a company keycard encoder machine.
- **Without Storage State:** Every employee has to stand in line at HR, fill out identity verification forms, and present passports every single morning before entering the building.
- **With Storage State (`storageState`):** HR verifies the employee once on day one and prints a permanent RFID keycard (`state.json`). Every morning after, the employee holds the keycard against the reader (`browser.newContext({ storageState: 'state.json' })`) and walks right through security.

---

## How It Works

1. **Authentication Phase (Setup):** Perform UI login inside `test.beforeAll()`.
2. **Save Storage State to Disk:** Capture cookies and local storage:
   `await context.storageState({ path: 'state.json' });`
3. **Reuse Storage State in New Contexts:** Inject `state.json` when creating new browser contexts:
   `const context = await browser.newContext({ storageState: 'state.json' });`
4. **Global Configuration Setup:** Alternatively, configure `storageState: 'state.json'` globally in `playwright.config.js`.

---

## Key Characteristics

- **Zero Third-Party Dependencies:** Built natively into `@playwright/test`.
- **Full Cookie & Storage Capture:** Saves both HTTP cookies and HTML5 `localStorage` / `sessionStorage`.
- **Global Config Integration:** Applies logged-in session state across all spec files automatically.

---

## Common Mistakes

- **Saving storage state BEFORE login completes:** Calling `context.storageState()` immediately after clicking submit, before server response sets session tokens or cookies.
- **Committing expired storage state files to Git:** Committing `state.json` containing short-lived production tokens to git repositories. Always generate `state.json` dynamically in setup projects.

---

## Canonical Code Example

```javascript
// Global storage state capture and reuse across test specs
const { test, expect, browser } = require('@playwright/test');

let webContext;

test.beforeAll(async ({ browser }) => {
  // 1. Create temporary context and perform UI login
  const setupContext = await browser.newContext();
  const page = await setupContext.newPage();
  await page.goto('https://example.com/login');

  await page.fill('#username', 'user@test.com');
  await page.fill('#password', 'Password123');
  await page.click('#login-btn');

  // Wait for login completion and session storage population
  await page.waitForURL('**/dashboard');

  // 2. Save authenticated cookies and local storage to state.json file on disk
  await setupContext.storageState({ path: 'state.json' });

  // 3. Create reusable global web context injected with state.json
  webContext = await browser.newContext({ storageState: 'state.json' });
});

test('test spec 1 reusing authenticated storage state', async () => {
  // Open page inside webContext (bypasses login screen!)
  const page = await webContext.newPage();
  await page.goto('https://example.com/dashboard');
  await expect(page.locator('.user-header')).toBeVisible();
});

test('test spec 2 reusing authenticated storage state', async () => {
  const page = await webContext.newPage();
  await page.goto('https://example.com/settings');
  await expect(page.locator('.settings-panel')).toBeVisible();
});
```

---

## Key Takeaways

- Capture logged-in state using `context.storageState({ path: 'state.json' })`.
- Reuse state across new browser contexts using `browser.newContext({ storageState: 'state.json' })`.
- Eliminates repeated logins across multi-spec test runs.

---

## Related

- [[pw-hybrid-api-ui-auth-bypass-token-injection]] — Token injection
- [[pw-browser-context-storage]] — Browser Context storage hierarchy
- [[MOC - Playwright Framework Design]]
