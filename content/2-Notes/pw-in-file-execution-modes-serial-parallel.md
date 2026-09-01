---
id: 202608152103
title: In-File Execution Mode Control with test.describe.configure
aliases:
  - test.describe.configure
  - mode serial
  - mode parallel
  - in-file test execution modes
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/execution
date_created: 2026-08-15
mastery_level: 1
---

# In-File Execution Mode Control with test.describe.configure

---

> **TL;DR:** `test.describe.configure({ mode: 'parallel' })` forces tests within a single spec file to run concurrently across worker processes, while `{ mode: 'serial' }` enforces sequential order and skips remaining tests if a preceding step fails.

---

## Why This Exists

By default, Playwright runs tests inside a single spec file serially (one after another), but continues running downstream tests even if an earlier step fails. For independent test cases (like 3 visual snapshot comparisons), running them sequentially wastes execution time. Conversely, for tightly coupled multi-step workflows (like Order Checkout $\rightarrow$ Payment Verification), running downstream steps after a step fails creates misleading test failures. `test.describe.configure()` provides explicit in-file mode control.

---

## Mental Model

Imagine an automated vehicle assembly line.
- **In-File Parallel Mode (`mode: 'parallel'`):** 3 independent robots paint 3 different car doors simultaneously. If Robot #1 takes longer, Robot #2 and #3 finish independently.
- **In-File Serial Mode (`mode: 'serial'`):** Robot #1 attaches the chassis, Robot #2 drops in the engine, and Robot #3 attaches wheels. If Robot #1 fails to attach the chassis (`test 1 fail`), Robot #2 and #3 immediately halt (`test 2 and 3 skipped`), preventing damaged engines from dropping onto empty floor space.

---

## How It Works

### Parallel Mode (`mode: 'parallel'`)
Spins up multiple worker threads for tests defined within the same file:
```javascript
test.describe.configure({ mode: 'parallel' });

test('visual check 1', async ({ page }) => { /* ... */ });
test('visual check 2', async ({ page }) => { /* ... */ });
```

### Serial Inter-Dependent Mode (`mode: 'serial'`)
Enforces strict sequential order and automatically skips downstream tests if a step fails:
```javascript
test.describe.configure({ mode: 'serial' });

test('step 1: create order', async ({ page }) => { /* ... */ });
test('step 2: verify order in history', async ({ page }) => { /* ... */ }); // Skipped if step 1 fails!
```

---

## Key Characteristics

- **Zero Cascading False Failures:** Mode `serial` marks dependent downstream steps as `Skipped` rather than `Failed` when upstream steps fail.
- **File-Level Granularity:** Overrides global `playwright.config.js` settings for a specific test file.
- **Worker Acceleration:** Mode `parallel` reduces total file execution duration by running independent cases concurrently.

---

## Common Mistakes

- **Enabling `mode: 'parallel'` on stateful tests:** Running tests concurrently in the same file when test 2 modifies database rows expected by test 1. Tests fail due to race conditions!
- **Using `mode: 'serial'` without isolation design:** Writing serial tests where test state contaminates global application settings. Always reset state between serial blocks.

---

## Canonical Code Example

```javascript
// In-file serial mode configuration for inter-dependent test flows
const { test, expect } = require('@playwright/test');

// Enforce serial inter-dependency: If step 1 fails, step 2 & 3 are SKIPPED
test.describe.configure({ mode: 'serial' });

let page;

test.beforeAll(async ({ browser }) => {
  page = await browser.newPage();
});

test('step 1: login to portal', async () => {
  await page.goto('https://example.com/login');
  await page.fill('#username', 'admin');
  await page.fill('#password', 'password123');
  await page.click('#login-btn');
  await expect(page.locator('.dashboard')).toBeVisible();
});

test('step 2: create new customer order', async () => {
  // If step 1 failed, this test is automatically SKIPPED
  await page.click('#create-order-btn');
  await expect(page.locator('.order-success')).toBeVisible();
});

test.afterAll(async () => {
  await page.close();
});
```

---

## Key Takeaways

- Use `test.describe.configure({ mode: 'parallel' })` to run independent tests inside a single file concurrently.
- Use `test.describe.configure({ mode: 'serial' })` for inter-dependent step workflows to skip downstream tests if an earlier step fails.
- Prevents misleading cascading test failures in HTML reports.

---

## Related

- [[pw-parallel-serial-worker-execution-control]] — Global worker configuration
- [[pw-flaky-test-retries-detection]] — Automated retries
- [[MOC - Playwright Execution]]
