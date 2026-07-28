---
id: 202606131223
title: Playwright Config File
aliases:
  - playwright-config
  - global-setup-retries
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/architecture
date_created: 2026-06-13
mastery_level: 1
---

# Playwright Config File

> **TL;DR:** The `playwright.config.js` file is the **control tower** of your test suite. It defines global behaviors like how many parallel browsers to run, how many times to retry failed tests, and where to output failure diagnostics.

## Centralized Control

Instead of writing long, messy commands in the terminal (like `npx playwright test --workers=4 --retries=2 --headed`), Playwright looks for a file named `playwright.config.js` in the root of your project. This file stores your settings as a clean JavaScript object.

---

## Key Configuration Properties

### 1. Execution Controls
- **`workers`**: The number of parallel browser processes running tests. 
  * *Analogy:* If you have 100 packages to deliver, adding more delivery workers (parallel processes) gets the job done faster.
- **`fullyParallel`**: By default, Playwright runs files in parallel, but executes tests *inside* a single file sequentially. Setting this to `true` allows individual tests inside a single file to execute in parallel as well.
- **`retries`**: How many times to rerun a test if it fails.
  * *Why it exists:* Network glitches or slow responses can cause **test flakiness** (a test that fails once but passes when run again). Setting retries gives a test another chance to pass before marking the run as failed.

### 2. The `use` Object (Default Context Settings)
The `use` property defines the settings automatically applied to every `BrowserContext` spawned by your tests.
- **`baseURL`**: The main domain (e.g. `https://automationexercise.com`). In tests, you can navigate using relative paths: `await page.goto("/login")`.
- **`actionTimeout`**: The maximum time (in milliseconds) a click or fill action will wait for an element to be ready before failing (defaults to no limit; recommended setting is `10000` to `15000` ms).
- **`screenshot`**: Capture options like `'only-on-failure'` to save visual evidence without cluttering your storage on successful runs.

### 3. Projects (Cross-Browser Compatibility)
The `projects` array defines the environments your tests run in. You can configure the same test suite to run against Chromium, Firefox, WebKit (Safari), and mobile emulators in a single run.

---

## Canonical Config Example

Here is a professional, commented `playwright.config.js` file:

```javascript
// @ts-check
const { defineConfig, devices } = require("@playwright/test");

module.exports = defineConfig({
  // 1. Where Playwright looks for tests
  testDir: "./tests",

  // 2. Parallelism settings
  fullyParallel: true,
  // 2 workers on CI to prevent overloading pipeline servers, 4 locally
  workers: process.env.CI ? 2 : 4,

  // 3. Reliability settings
  retries: process.env.CI ? 2 : 0, // Retry twice on CI to handle flakiness
  timeout: 30000,                  // 30-second limit for a single test

  // 4. Reporting settings
  reporter: [
    ["list"], // Concise CLI logger
    ["html", { outputFolder: "reports/html-report", open: "never" }] // Rich HTML report
  ],

  // 5. Global browser context configurations
  use: {
    baseURL: "https://automationexercise.com",
    actionTimeout: 10000,            // Limit clicks/fills to 10s wait
    screenshot: "only-on-failure",   // Capture image on failure
    video: "retain-on-failure",      // Record video only when a test fails
    trace: "on-first-retry",         // Record tracing timeline on retry
  },

  // 6. Cross-browser targets (Projects)
  projects: [
    {
      name: "chromium",
      use: { ...devices["Desktop Chrome"] },
    },
    {
      name: "firefox",
      use: { ...devices["Desktop Firefox"] },
    },
    {
      name: "webkit",
      use: { ...devices["Desktop Safari"] },
    },
  ],
});
```

---

## Related
* [[pw-browser-context-and-page-model]] - How global context options are instantiated as isolated session objects.
* [[pw-parallel-vs-serial-execution]] - How workers coordinate parallel tests.
* [[pw-github-actions-integration]] - Tuning configuration parameters for headless pipeline runs.
