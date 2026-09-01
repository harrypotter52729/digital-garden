---
id: 202608152062
title: Parallel vs Serial Worker Execution Control
aliases:
  - parallel test execution
  - serial test execution
  - workers config
  - test.describe.configure
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/execution
date_created: 2026-08-15
mastery_level: 1
---

# Parallel vs Serial Worker Execution Control

---

> **TL;DR:** Playwright executes different spec files in parallel across OS worker threads (`workers: 4`) while running tests within the same spec file serially by default—controlled globally via `workers` config or locally via `test.describe.configure({ mode: 'parallel' })`.

---

## Why This Exists

Running 200 test spec files sequentially one after another takes hours. Playwright maximizes CPU utilization by spinning up multiple parallel worker threads (`workers: 4`), assigning distinct spec files to separate worker processes. However, when tests within a single file share database state, they must run serially. Playwright provides fine-grained control over parallel vs serial execution.

---

## Mental Model

Imagine a bank with multiple teller windows.
- **Parallel Workers (`workers: 4`):** Opening 4 separate teller windows simultaneously. 4 different customers (separate spec files) walk up to 4 windows at the exact same time, getting served in 1/4th the total time.
- **Serial File Execution (Default):** Customer #1 at Window #1 has a 3-step deposit process. Step 1 (login), Step 2 (deposit), and Step 3 (receipt check) must happen in strict sequential order (serial mode) at that single teller window.

---

## How It Works

### Default Execution Rules
1. **Across Spec Files:** Parallel execution (Playwright assigns spec files across available worker threads).
2. **Within a Single Spec File:** Serial sequential execution (Tests inside `login.spec.js` run `test 1` $\rightarrow$ `test 2` $\rightarrow$ `test 3`).

### Configuration Overrides
- **Global Worker Control:**  
  Set worker thread counts in `playwright.config.js`:
  ```javascript
  module.exports = defineConfig({
    workers: process.env.CI ? 2 : 4, // Run 4 parallel workers locally
  });
  ```
- **In-File Parallelization:** Force tests within a single spec file to run in parallel:
  ```javascript
  test.describe.configure({ mode: 'parallel' });
  ```
- **In-File Serial Enforcement:** Force inter-dependent tests to stop if a preceding test fails:
  ```javascript
  test.describe.configure({ mode: 'serial' });
  ```

---

## Key Characteristics

- **Worker Thread Isolation:** Each worker runs in a separate Node.js OS process with an isolated browser context.
- **Scalable Execution:** Scaling workers from 1 to 4 cuts total test execution time by ~70%.
- **Serial Inter-Dependency Mode:** Mode `serial` automatically skips downstream tests in a spec file if a step fails.

---

## Common Mistakes

- **Enabling `mode: 'parallel'` on tests sharing global state:** Forcing in-file parallel execution on specs where `test 2` relies on database records created by `test 1`. Tests fail due to race conditions!
- **Setting worker counts higher than CPU core capacity:** Setting `workers: 16` on a 4-core machine. System thrashing degrades performance; match workers to available CPU cores.

---

## Canonical Config & Spec Code

### `playwright.config.js`

```javascript
const { defineConfig } = require('@playwright/test');

module.exports = defineConfig({
  // Configure worker thread count (4 workers locally, 2 on CI)
  workers: process.env.CI ? 2 : 4,
  fullyParallel: true, // Forces all tests in all files to run fully parallel
});
```

### Spec File: `tests/independentTests.spec.js`

```javascript
// Forcing in-file parallel execution for independent test cases
const { test, expect } = require('@playwright/test');

// Force all tests in this file to run in parallel across worker threads
test.describe.configure({ mode: 'parallel' });

test('independent search test 1', async ({ page }) => {
  await page.goto('https://example.com/search?q=phone');
  await expect(page.locator('.results')).toBeVisible();
});

test('independent search test 2', async ({ page }) => {
  await page.goto('https://example.com/search?q=laptop');
  await expect(page.locator('.results')).toBeVisible();
});
```

---

## Key Takeaways

- Spec files run in parallel across worker threads (`workers: 4`) by default.
- Individual tests inside a single spec file run serially by default.
- Use `test.describe.configure({ mode: 'parallel' })` or `fullyParallel: true` to parallelize test cases inside spec files.

---

## Related

- [[pw-flaky-test-retries-detection]] — Retrying failed worker tests
- [[pw-cli-execution-flags]] — Setting workers via CLI flags
- [[MOC - Playwright Execution]]
