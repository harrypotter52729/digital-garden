---
id: 202608152060
title: Global Test Execution Artifact Capture Settings
aliases:
  - artifact capture settings
  - screenshot only-on-failure
  - video retain-on-failure
  - trace on-first-retry
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/debugging
date_created: 2026-08-15
mastery_level: 1
---

# Global Test Execution Artifact Capture Settings

---

> **TL;DR:** Configuring `screenshot: 'only-on-failure'`, `video: 'retain-on-failure'`, and `trace: 'on-first-retry'` inside `playwright.config.js` captures rich failure evidence while automatically deleting media files for passing tests to conserve CI storage.

---

## Why This Exists

Recording full video files (`video: 'on'`), screenshots, and trace zip archives for every passing test in a suite of 500 specs generates tens of gigabytes of useless media files—exhausting continuous integration (CI) disk storage and slowing down build pipelines. Playwright provides conditional artifact capture settings (`only-on-failure`, `retain-on-failure`, `on-first-retry`) to record debugging media strictly when tests fail.

---

## Mental Model

Imagine a security dashboard camera on a fleet of delivery trucks.
- **`video: 'on'` (Storage Exhaustion):** Recording 24/7 high-definition video for 1,000 trucks drives. By the end of the day, company hard drives run out of space, storing 99.9% routine uneventful driving footage.
- **`video: 'retain-on-failure'` (Smart Capture):** Keeping video buffers in RAM memory while driving. If a truck arrives safely (`test pass`), the buffer is discarded. If a fender bender occurs (`test failure`), the camera saves the 30-second accident footage to disk for review.

---

## How It Works

Configure conditional artifact modes inside `playwright.config.js`:

```javascript
module.exports = defineConfig({
  use: {
    // Captures full-page screenshot ONLY when a test fails
    screenshot: 'only-on-failure',

    // Records WebM video during execution, but deletes video files if test passes
    video: 'retain-on-failure',

    // Captures trace.zip archives ONLY when a test fails and is retried
    trace: 'on-first-retry',
  },
});
```

---

## Artifact Setting Options Matrix

| Setting Mode | `screenshot` | `video` | `trace` | Storage Overhead |
| :--- | :--- | :--- | :--- | :--- |
| **`'off'`** | Never captures | Never records | Never traces | **Zero** |
| **`'on'`** | Captures all tests | Records all tests | Traces all tests | **Extremely High** |
| **`'only-on-failure'`** | Captures failing tests | N/A | Captures failing tests | **Low** |
| **`'retain-on-failure'`** | N/A | Keeps failing videos | N/A | **Low** |
| **`'on-first-retry'`** | N/A | Records on retry | Traces on retry | **Optimal for CI** |

---

## Key Characteristics

- **Automatic Disk Space Optimization:** Keeps CI storage lean by deleting passing test video recordings.
- **Seamless HTML Report Embedding:** Failing screenshots, videos, and trace zip links are attached directly to failed rows in Playwright HTML reports.
- **Zero Test Spec Pollution:** Applied globally in `playwright.config.js` without writing screenshot code in spec files.

---

## Common Mistakes

- **Setting `video: 'on'` globally in large CI suites:** Causing CI agent disk space to fill up during nightly test runs. Always use `'retain-on-failure'` or `'on-first-retry'`.
- **Forgetting that `retain-on-failure` records during execution:** Expecting zero CPU overhead for passing tests. Playwright records video in RAM during execution and deletes the file upon test pass; use `'off'` if CPU resources are constrained.

---

## Canonical Config Example

```javascript
// playwright.config.js optimized for CI disk storage and debugging evidence
const { defineConfig } = require('@playwright/test');

module.exports = defineConfig({
  // Global options applied across all projects
  use: {
    // 1. Capture full-page screenshot on test failure
    screenshot: 'only-on-failure',

    // 2. Retain video recordings strictly for failing tests
    video: 'retain-on-failure',

    // 3. Record trace.zip archives on first test retry
    trace: 'on-first-retry',
  },

  // Configure HTML reporter output folder
  reporter: [['html', { open: 'never' }]],
});
```

---

## Key Takeaways

- Set `screenshot: 'only-on-failure'` to capture failure screenshots automatically.
- Set `video: 'retain-on-failure'` to save video recordings for failing tests while discarding passing videos.
- Set `trace: 'on-first-retry'` for optimal CI execution storage and post-mortem debugging.

---

## Related

- [[pw-html-reporting-artifacts]] — HTML reporter and artifact analysis
- [[pw-trace-viewer-debugging]] — Analyzing trace archives
- [[MOC - Playwright Framework Design]]
