---
id: 202607282013
title: Configuring Custom & HTML Reporters in Playwright
aliases:
  - playwright reporters
  - allure reporter playwright
  - html reporter
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/execution-cicd
date_created: 2026-07-28
mastery_level: 1
---

# Configuring Custom & HTML Reporters in Playwright

> **TL;DR:** Playwright reporters translate test execution results into rich visual reports (HTML, Allure, JUnit)—capturing traces, failure videos, and step-by-step screenshots for instant CI/CD debugging!

---

## Canonical Code Example

```javascript
// playwright.config.js snippet
import { defineConfig } from '@playwright/test';

export default defineConfig({
  reporter: [
    ['list'],
    ['html', { outputFolder: 'playwright-report', open: 'never' }],
    ['json', { outputFile: 'reports/results.json' }],
    ['junit', { outputFile: 'reports/results.xml' }]
  ],
  use: {
    trace: 'retain-on-failure',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure'
  }
});
```

---

## Related
- [[pw-html-reporter-and-traces]] — Tracing and HTML reports.
- [[pw-github-actions-integration]] — CI/CD integration.
- [[MOC - Playwright Framework Design]] — Framework design MOC.
