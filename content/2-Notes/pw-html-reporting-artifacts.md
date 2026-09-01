---
id: 202608152022
title: HTML Test Reporting and Execution Artifact Analysis
aliases:
  - html reporter
  - test artifacts
  - show-report
  - retain-on-failure
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/debugging
date_created: 2026-08-15
mastery_level: 1
---

# HTML Test Reporting and Execution Artifact Analysis

---

> **TL;DR:** Playwright HTML Reporter is an automated medical diagnostic report generated after every test run—it summarizes pass/fail metrics and attaches failure screenshots, execution video recordings, and trace zip archives directly to failing test rows.

---

## Why This Exists

Parsing console logs across dozens of test executions in continuous integration environments is slow and labor-intensive. Playwright provides a built-in **HTML Reporter** (`reporter: ['html']`) that compiles execution data, step timings, failure stack traces, screenshots, video recordings, and trace archives into a standalone, interactive HTML dashboard.

---

## Mental Model

Imagine a vehicle safety inspection station. Instead of handing you a slip of paper with a handwritten "Failed" note, the mechanic hands you an interactive digital dashboard (`index.html`). Clicking on a failed brake test reveals high-definition photos (screenshots), a video replay of the brake test, and diagnostic sensor logs (`trace.zip`).

---

## How It Works

1. **Reporter Configuration:** Declare `reporter: [['html', { open: 'never' }]]` inside `playwright.config.js`.
2. **Artifact Generation:** Configure artifact capture modes inside `use`:
   - `screenshot: 'only-on-failure'` (Captures full-page screenshot on test failure).
   - `video: 'retain-on-failure'` (Saves WebM video recording of failing tests).
   - `trace: 'on-first-retry'` (Saves trace zip archives for retried test failures).
3. **Report Inspection:** Open the generated report folder (`playwright-report/index.html`) using `npx playwright show-report`.

---

## Key Characteristics

- **Zero Third-Party Dependencies:** Native HTML reporter built directly into `@playwright/test`.
- **Embedded Artifact Viewer:** Play video recordings, view failure screenshots, and download trace zip files directly inside the browser report interface.
- **CI Storage Optimization:** Configuring `retain-on-failure` for videos and screenshots prevents disk space exhaustion in CI agents.

---

## Common Mistakes

- **Forgetting `open: 'never'` in CI configuration:** By default, Playwright attempts to open the HTML report automatically when a test fails. In headless CI environments, this blocks terminal build execution! Always set `open: 'never'` in config.
- **Capturing video/screenshots for ALL passing tests:** Setting `video: 'on'` and `screenshot: 'on'` generates gigabytes of unused media files for passing tests. Use `'only-on-failure'` or `'retain-on-failure'`.

---

## Canonical Code Example

```javascript
// playwright.config.js artifact configuration
const { defineConfig } = require('@playwright/test');

module.exports = defineConfig({
  // Configure standalone HTML reporter
  reporter: [
    ['html', { outputFolder: 'playwright-report', open: 'never' }],
    ['list']
  ],

  use: {
    // Artifact capture rules (Optimized for storage and CI)
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
    trace: 'on-first-retry',
  },
});
```

---

## Key Takeaways

- HTML Reporter compiles interactive pass/fail execution reports automatically.
- Attaches screenshots, WebM video recordings, and trace archives to failing test rows.
- Use `only-on-failure` and `retain-on-failure` settings to optimize CI disk storage.

---

## Related

- [[pw-trace-viewer-debugging]] — Analyzing trace archives
- [[pw-configuration-file]] — Reporter configuration in config
- [[MOC - Playwright Execution]]
