---
id: 202608152105
title: Allure Reporting Integration and HTML Artifact Publishing
aliases:
  - allure reporting
  - allure-playwright
  - allure generate
  - allure open
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/debugging
date_created: 2026-08-15
mastery_level: 1
---

# Allure Reporting Integration and HTML Artifact Publishing

---

> **TL;DR:** Installing `allure-playwright` and configuring `reporter: [['line'], ['allure-playwright']]` generates interactive, pie-chart-rich Allure HTML dashboard reports with embedded failure screenshots, console logs, and step breakdowns.

---

## Why This Exists

While Playwright's built-in HTML reporter provides detailed single-test trace inspection, enterprise QA leadership often requires high-level executive dashboards featuring visual pie charts, historical trend metrics, and severity categorization. **Allure Reporter** is a multi-language reporting framework that integrates with Playwright to generate polished test reports.

---

## Mental Model

Imagine presenting test results to executive leadership.
- **Console Log Output:** Handing executives a 200-page raw text printout of terminal logs. Finding failure trends requires manual reading.
- **Allure Interactive Dashboard:** Presenting an interactive color-coded dashboard with pie charts showing 85% Pass / 15% Fail, filtered by severity tags, with clickable drill-downs showing exact failure screenshots and video recordings for broken specs.

---

## How It Works

```text
┌───────────────────────────────────────┐
│ 1. Run Playwright Test Suite          │
│    npx playwright test                │
│    --reporter=line,allure-playwright  │
└───────────────────────────────────────┘
                    │
                    ▼ Output JSON Data
┌───────────────────────────────────────┐
│ 2. Generate allure-results/ Folder    │
│    (Raw JSON metrics & screenshots)   │
└───────────────────────────────────────┘
                    │
                    ▼ Compile HTML
┌───────────────────────────────────────┐
│ 3. Allure CLI Compile & Serve         │
│    allure generate allure-results     │
│    allure open allure-report          │
└───────────────────────────────────────┘
```

---

## Installation & Setup

1. **Install Allure Playwright Adapter:**
   `npm install --save-dev allure-playwright`
2. **Install Allure Commandline Utility (Globally or via npm):**
   `npm install --save-dev allure-commandline`
3. **Configure Reporter in `playwright.config.js`:**
   ```javascript
   module.exports = defineConfig({
     reporter: [
       ['line'],
       ['allure-playwright', { outputFolder: 'allure-results' }],
     ],
   });
   ```

---

## CLI Generation Workflow Commands

```bash
# 1. Run Playwright tests and generate raw allure-results JSON data
npx playwright test --reporter=line,allure-playwright

# 2. Compile raw allure-results into polished HTML report folder
npx allure generate allure-results --clean -o allure-report

# 3. Spin up local Web Server to view Allure HTML dashboard
npx allure open allure-report
```

---

## Key Characteristics

- **Executive Pie-Chart Dashboard:** Visualizes test pass/fail ratios, duration statistics, and severity metrics.
- **Rich Failure Artifacts:** Automatically embeds failure screenshots, execution videos, and console logs directly into step trees.
- **CI/CD Integration:** Integrates natively with Jenkins, GitHub Actions, and GitLab CI report publishers.

---

## Common Mistakes

- **Opening `allure-report/index.html` directly in a browser via file system:** Double-clicking `index.html` directly from Windows Explorer (`file:///...`). Browser CORS security policies block AJAX data loading, causing a blank report! Always use `npx allure open allure-report` to serve over HTTP.
- **Forgetting the `--clean` flag when re-generating reports:** Running `allure generate` without `--clean`, appending old historical run artifacts to fresh test runs.

---

## Canonical Config Example

```javascript
// playwright.config.js configured for dual HTML & Allure reporting
const { defineConfig } = require('@playwright/test');

module.exports = defineConfig({
  reporter: [
    ['html', { open: 'never' }],
    [
      'allure-playwright',
      {
        detail: true,
        outputFolder: 'allure-results',
        suiteTitle: false,
      },
    ],
  ],
  use: {
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
    trace: 'on-first-retry',
  },
});
```

---

## Key Takeaways

- Install `allure-playwright` and add `['allure-playwright']` to the `reporter` config array.
- Compile raw JSON results into HTML using `npx allure generate allure-results --clean -o allure-report`.
- Always view Allure reports using `npx allure open allure-report` to bypass browser CORS file restrictions.

---

## Related

- [[pw-global-artifact-capture-settings]] — Artifact capture configuration
- [[jenkins-ci-playwright-integration]] — Publishing reports in Jenkins
- [[MOC - Playwright Framework Design]]
