---
id: 202608152004
title: Playwright Configuration File Architecture and Options
aliases:
  - playwright.config.js
  - playwright configuration
  - defineconfig
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/environment
date_created: 2026-08-15
mastery_level: 1
---

# Playwright Configuration File Architecture and Options

---

> **TL;DR:** `playwright.config.js` is the central control tower for your entire test automation framework—it dictates which browsers to launch, timeouts, retries, reporting formats, base URLs, and environment variables.

---

## Why This Exists

Hardcoding test configurations (e.g., target browser, headless flags, default timeouts, base URLs) directly inside individual test spec files makes test suites unmaintainable. Playwright centralizes all environment settings, execution parameters, browser matrix projects, and reporting definitions into a single declarative file: `playwright.config.js` (or `.ts`).

---

## Mental Model

Imagine an airport control tower. Instead of pilots deciding independently which runway to land on, what altitude to hold, or which radio frequency to monitor, the control tower issues a unified flight plan (`playwright.config.js`). Every test spec departing from the runway follows the exact timeout boundaries, browser engine specifications, and reporting protocols defined by the tower.

---

## How It Works

1. **Configuration Declaration:** Export a configuration object wrapped inside `defineConfig({ ... })` from `@playwright/test`.
2. **Key Structural Sections:**
   - `testDir`: Directory path where Playwright scans for spec files.
   - `timeout`: Global timeout limit for individual test cases (default 30s).
   - `expect`: Assertion timeout limit (default 5s).
   - `use`: Global options applied to all test contexts (e.g., `baseURL`, `headless`, `trace`, `screenshot`).
   - `projects`: Multi-browser / multi-environment execution matrix definitions (Chromium, Firefox, WebKit, Mobile Viewports).
   - `reporter`: Reporting formats (HTML, Line, Dot, Allure, JUnit).

---

## Key Characteristics

- **Multi-Project Matrix:** Run identical test suites across multiple browsers and device emulations concurrently.
- **Environment Overrides:** Seamlessly swap base URLs and credentials based on environment variables (`process.env.STAGING_URL`).
- **Module Export Standard:** Compatible with ES modules (`export default defineConfig(...)`) and CommonJS (`module.exports = { ... }`).

---

## Common Mistakes

- **Setting `headless: false` in checked-in configuration files:** Hardcoding headful execution causes CI/CD build agents (which lack display servers) to fail.
- **Overlapping global timeouts and action timeouts:** Setting global test timeout lower than individual action timeouts causes ambiguous test cancellation stack traces.

---

## Best Practices

- Keep `headless: true` by default in `playwright.config.js` and use CLI flags (`--headed`) for local debugging.
- Use `defineConfig` for full TypeScript type-checking and IntelliSense autocomplete support.

---

## Canonical Code Example

```javascript
// playwright.config.js - Production-grade configuration structure
const { defineConfig, devices } = require('@playwright/test');

module.exports = defineConfig({
  // Directory containing spec files
  testDir: './tests',

  // Maximum time one test can run (30 seconds)
  timeout: 30 * 1000,

  // Assertion timeout configuration (5 seconds)
  expect: {
    timeout: 5000,
  },

  // Parallel execution settings
  fullyParallel: true,
  workers: process.env.CI ? 2 : undefined,

  // Reporter selection
  reporter: [
    ['html', { open: 'never' }],
    ['list']
  ],

  // Global context settings
  use: {
    baseURL: process.env.BASE_URL || 'https://staging.example.com',
    headless: true,
    viewport: { width: 1280, height: 720 },
    ignoreHTTPSErrors: true,
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
    trace: 'on-first-retry',
  },

  // Multi-browser execution matrix
  projects: [
    {
      name: 'Desktop Chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'Desktop Firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'Desktop Safari',
      use: { ...devices['Desktop Safari'] },
    },
    {
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] },
    },
  ],
});
```

---

## Key Takeaways

- `playwright.config.js` acts as the single source of truth for test runner behavior.
- Manages timeouts, browser engines (`projects`), reporting, and global context options (`use`).
- Enables seamless environment parameterization via `process.env`.

---

## Related

- [[pw-project-initialization]] — Generated during initial setup
- [[pw-cli-execution-flags]] — Overriding config options via CLI
- [[MOC - Playwright Framework Design]]
