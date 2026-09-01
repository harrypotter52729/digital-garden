---
id: 202608152061
title: Automated Retries and Flaky Test Detection
aliases:
  - test retries
  - retries config
  - flaky test detection
  - retry failed tests
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/execution
date_created: 2026-08-15
mastery_level: 1
---

# Automated Retries and Flaky Test Detection

---

> **TL;DR:** Configuring `retries: 2` in `playwright.config.js` automatically re-executes failing tests up to 2 times—marking tests that pass on retry as "Flaky" in HTML reports to isolate network instability from true bugs.

---

## Why This Exists

Automated test suites running in CI environments often suffer from environmental flakiness (temporary network lag, slow server responses, or micro-glitches). When a test fails on its initial run due to network lag but passes on a immediate second run, failing the entire CI build pipeline creates unnecessary developer frustration. Playwright provides native **Automated Test Retries** (`retries: N`).

---

## Mental Model

Imagine a bowler in a bowling tournament.
- **Without Retries:** If the bowler slips on their shoes on the first roll, they receive an automatic zero and are disqualified from the tournament immediately.
- **With Retries (`retries: 1`):** If the bowler slips on the first roll (`test failure`), the referee grants a second attempt (`retry 1`). If the bowler knocks down all pins on the second roll, the referee records a pass, but flags the score sheet with an asterisk: **"Passed on Retry (Flaky)"** so mechanics can inspect the slippery shoe soles later.

---

## How It Works

1. **Configure Retry Count in Config:**
   ```javascript
   module.exports = defineConfig({
     retries: 2, // Re-runs failed tests up to 2 times
   });
   ```
2. **Execution Flow:**
   - **Initial Attempt (Attempt #0):** Test fails due to element timeout or network lag.
   - **Retry #1 (Attempt #1):** Playwright creates a fresh browser context and re-runs the spec.
   - **If Retry #1 Passes:** Test execution completes successfully and logs status as **Flaky** in HTML reports.
   - **If All Retries Fail:** Test is logged as **Failed**.

---

## Key Characteristics

- **Fresh Browser Context Per Retry:** Each retry execution starts inside a completely isolated, clean browser context.
- **Flaky Categorization:** Distinguishes between true hard failures (failed all retries) and flaky infrastructure issues (passed on retry).
- **CI vs Local Customization:** Configure zero retries locally for fast feedback, and 2 retries in CI pipelines (`retries: process.env.CI ? 2 : 0`).

---

## Common Mistakes

- **Setting excessively high retry counts (e.g., `retries: 10`):** Setting retries to 10 hides underlying application bugs and balloons CI build execution times. Keep retries between 1 and 2.
- **Ignoring "Flaky" test warnings in reports:** Treating flaky test passes as clean passes without investigating why the initial run failed.

---

## Canonical Config Example

```javascript
// playwright.config.js automated retry configuration
const { defineConfig } = require('@playwright/test');

module.exports = defineConfig({
  // Configure retries: 2 retries in CI environments, 0 in local development
  retries: process.env.CI ? 2 : 0,

  use: {
    // Record trace archives ONLY when a test fails and gets retried
    trace: 'on-first-retry',
  },

  reporter: [['html', { open: 'never' }]],
});
```

---

## Key Takeaways

- Configure `retries: 2` to automatically re-execute failed tests.
- Fresh browser contexts are instantiated for every retry attempt.
- Tests that pass on retry are categorized as "Flaky", distinguishing network hiccups from genuine code defects.

---

## Related

- [[pw-trace-viewer-debugging]] — Tracing retried test failures
- [[pw-global-artifact-capture-settings]] — `on-first-retry` trace settings
- [[MOC - Playwright Execution]]
