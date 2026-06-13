---
id: 202606131241
title: HTML Reporter & Trace Viewer
aliases:
  - playwright-traces
  - trace-viewer
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/execution-cicd
date_created: 2026-06-13
mastery_level: 1
---

# HTML Reporter & Trace Viewer

> **TL;DR:** Debugging headless test failures in a terminal is difficult. Playwright handles diagnostics using a **Trace Viewer** that records a full "black box" zip file containing step-by-step screenshots, network requests, console logs, and action positions. You can analyze these logs through a rich, interactive **HTML Reporter** interface.

## The Flight Black Box Analogy

Imagine building a model airplane and launching it:

```
Headless CLI Output (No Visuals):
"Run failed. Model crashed at T+15 seconds."
(You have no idea if the engine quit, the rudder broke, or a wind gust hit it).

Playwright Trace (Flight Black Box):
You open the flight recorder telemetry to view:
1. Video replay of the flight.
2. Timestamps of when every toggle was switched.
3. Airspeed and battery voltage charts (Network calls / console logs).
4. Laser pointer dot showing exactly where the pilot was looking (Click coordinates).
```

Playwright's Trace Viewer does exactly this. It captures the entire execution timeline, letting you inspect the DOM state, see action targets, and browse API call logs for every single action.

---

## The Diagnostics Toolkit

When a test runs with tracing enabled, Playwright records:
- **Action Logs:** Exactly what code ran, how long it took, and whether actionability checks passed.
- **Before/After DOM Snapshots:** An interactive, inspectable HTML DOM snapshot showing the page state *before*, *during*, and *after* each mouse click or keystroke.
- **Network Panel:** Every HTTP request and response payload sent during the action.
- **Console Log:** Stderr/Stdout outputs and uncaught browser JavaScript errors.

---

## Configuring Diagnostic Captures

Generating traces on every test takes up CPU and disk storage. The best practice is to collect them conditionally (e.g., only on retries or failures).

### Configuration Options:
- **`'on'`**: Record a trace for every test run.
- **`'on-first-retry'`**: Only record a trace when a failing test is rerun (keeps initial runs fast, records only when needed).
- **`'retain-on-failure'`**: Record trace for every run, but delete it if the test passes (perfect for CI/CD pipelines).

---

## Canonical Code Example

This setup shows how to configure your config file to capture diagnostic zip files and launch the Trace Viewer locally.

### 1. Enabling Tracing in `playwright.config.js`

```javascript
// @ts-check
const { defineConfig } = require("@playwright/test");

module.exports = defineConfig({
  // Output folder for test run artifacts (traces, videos, screenshots)
  outputDir: "test-results/",

  reporter: [
    // Generates a self-contained HTML folder inside 'playwright-report/'
    ["html", { outputFolder: "playwright-report", open: "never" }]
  ],

  use: {
    // Record traces only when a test fails on the first run or retries
    trace: "retain-on-failure",
    screenshot: "only-on-failure",
    video: "retain-on-failure"
  }
});
```

### 2. Inspecting a Trace File

When a test fails, Playwright saves a `.zip` file inside the `test-results/` folder (e.g., `test-results/login-should-fail-chromium/trace.zip`).

To open and explore this file, run the following command in your terminal:

```bash
# Open the trace file in Playwright's local Trace Viewer app
npx playwright show-trace test-results/login-should-fail-chromium/trace.zip
```

Alternatively, you can drag and drop your `trace.zip` file directly into [trace.playwright.dev](https://trace.playwright.dev/) in any browser (it processes the zip locally on your machine without uploading it to external servers).

---

## Related
* [[pw-playwright-config-file]] - Customizing project-level directories and reporter configurations.
* [[pw-web-first-assertions]] - Reviewing timeline snapshots inside Trace Viewer to identify why assertion polling timed out.
