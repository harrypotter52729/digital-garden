---
id: 202608152106
title: Custom npm Script Automation in package.json
aliases:
  - npm scripts
  - package.json scripts
  - npm run automation
tags:
  - type/concept
  - status/processing
  - tool/node
  - lang/js
  - topic/environment
date_created: 2026-08-15
mastery_level: 1
---

# Custom npm Script Automation in package.json

---

> **TL;DR:** Defining alias commands inside the `"scripts"` block of `package.json` (e.g., `"test:web": "npx playwright test --grep @web"`) allows team members and CI pipelines to execute complex test configurations using short commands like `npm run test:web`.

---

## Why This Exists

Remembering long CLI commands (`npx playwright test --config=playwright.config.js --project=chromium --grep "@smoke"`) creates friction and leads to typos across team members. Defining shortcut aliases inside `package.json` under the `"scripts"` dictionary encapsulates complex CLI flags into standardized, short commands (`npm run test:smoke`).

---

## Mental Model

Imagine speed-dial buttons on an office desk phone.
- **Manual CLI Typing:** Dialing a 15-digit international phone number (`npx playwright test --config=... --project=... --grep=...`) from memory every single time you want to place a call.
- **Custom `package.json` npm Scripts:** Pressing Speed-Dial Button #1 (`npm run test:smoke`). The phone system handles dialing the 15-digit international sequence automatically in the background.

---

## How It Works

1. **Define Script Block inside `package.json`:**
   ```json
   {
     "name": "playwright-framework",
     "version": "1.0.0",
     "scripts": {
       "test": "npx playwright test",
       "test:regression": "npx playwright test",
       "test:web": "npx playwright test --grep @web",
       "test:api": "npx playwright test --grep @api",
       "test:safari": "npx playwright test --config=playwright.config.js --project=webkit",
       "report:allure": "npx allure generate allure-results --clean -o allure-report && npx allure open allure-report"
     }
   }
   ```
2. **Execute via CLI:**
   - Execute Web Tests: `npm run test:web`
   - Execute API Tests: `npm run test:api`
   - Generate & Open Allure Report: `npm run report:allure`

---

## Key Characteristics

- **Cross-Platform Standardization:** Ensures Windows, macOS, and Linux CI workers execute identical CLI arguments.
- **Short Shortcut Aliases:** Encapsulates complex combinations of `--config`, `--project`, and `--grep` flags.
- **CI Build Pipeline Readiness:** CI tools (Jenkins, GitHub Actions) trigger standard npm commands (`npm run test:regression`).

---

## Common Mistakes

- **Forgetting `run` keyword for custom script names:** Typing `npm test:web` instead of `npm run test:web`. (Note: `npm test` is a built-in shorthand, but custom scripts require `npm run <script-name>`).
- **Hardcoding machine-specific file paths in script strings:** Including Windows drive letters (`C:\Users\...`) inside `package.json` commands. Use relative workspace paths (`./utils/...`).

---

## Canonical `package.json` Script Example

```json
{
  "name": "playwright-automation-suite",
  "version": "1.0.0",
  "description": "Enterprise Playwright E2E and API Test Suite",
  "scripts": {
    "test": "npx playwright test",
    "test:web": "npx playwright test --grep @web",
    "test:api": "npx playwright test --grep @api",
    "test:smoke": "npx playwright test --grep @smoke --project=chromium",
    "test:headless": "npx playwright test --config=playwright.config.js",
    "report:show": "npx playwright show-report",
    "report:allure": "npx allure generate allure-results --clean -o allure-report && npx allure open allure-report"
  },
  "devDependencies": {
    "@playwright/test": "^1.40.0",
    "allure-commandline": "^2.24.0",
    "allure-playwright": "^2.9.0"
  }
}
```

---

## Key Takeaways

- Define shortcut aliases in the `"scripts"` dictionary of `package.json`.
- Execute custom scripts using `npm run <script-name>`.
- Encapsulates complex `--grep`, `--project`, and `--config` flags for clean CI integration.

---

## Related

- [[pw-test-tagging-selective-execution]] — Test tagging and CLI flags
- [[jenkins-ci-playwright-integration]] — Triggering npm scripts in Jenkins
- [[node-exceljs-project-setup]] — Node project setup
