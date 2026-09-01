---
id: 202608152107
title: Continuous Integration (CI) Integration with Jenkins
aliases:
  - Jenkins CI Playwright
  - Jenkins integration
  - parameterized Jenkins build
  - custom workspace Jenkins
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - tool/jenkins
  - topic/environment
date_created: 2026-08-15
mastery_level: 1
---

# Continuous Integration (CI) Integration with Jenkins

---

> **TL;DR:** Integrating Playwright with Jenkins involves configuring a Freestyle Job, setting workspace paths or Git repository URLs, and triggering npm commands (`npm run ${script}`) using parameterized dropdown choices.

---

## Why This Exists

Running test suites manually on local developer machines does not guarantee that code changes will pass in production. Continuous Integration (CI) servers like **Jenkins** automate test execution on scheduled cron timers, pull request check-ins, or post-deployment triggers—publishing pass/fail reports and alerting engineering teams instantly when regressions occur.

---

## Mental Model

Imagine an automated quality inspection conveyor belt in a car factory.
- **Local Developer Execution:** Mechanics inspecting car parts individually by hand at their workbench when they feel like it.
- **Jenkins CI Automation:** An automated quality inspection station at the end of the main assembly line. Every time a new car chassis rolls off the line (`code commit`), the station grabs the car, runs 50 automated safety diagnostic tests (`npm run test:regression`), records diagnostic charts, and emails the factory manager if any test fails.

---

## How It Works

```text
┌───────────────────────────────────────┐
│ 1. Code Commit / Scheduled Trigger    │
└───────────────────────────────────────┘
                    │
                    ▼
┌───────────────────────────────────────┐
│ 2. Jenkins Freestyle Job Invoked      │
│    (Custom Workspace / Git Checkout)  │
└───────────────────────────────────────┘
                    │
                    ▼
┌───────────────────────────────────────┐
│ 3. Execute Parameterized Build Step   │
│    npm run ${SCRIPT_NAME}             │
└───────────────────────────────────────┘
                    │
                    ▼
┌───────────────────────────────────────┐
│ 4. Publish Test Results & Reports     │
│    (Playwright HTML / Allure Report)  │
└───────────────────────────────────────┘
```

---

## Step-by-Step Jenkins Job Setup

1. **Create Freestyle Project:** Click **New Item** $\rightarrow$ Enter Job Name (e.g., `Playwright-Automation-Suite`) $\rightarrow$ Select **Freestyle project**.
2. **Configure Custom Workspace or Git SCM:**
   - **Local Workspace:** Under **Advanced**, check **Use custom workspace** and enter directory path (`g:\My Drive\Vault\...`).
   - **Git Repository:** Under **Source Code Management**, select **Git** and enter Repository URL (`https://github.com/org/repo.git`).
3. **Add Choice Parameter (Parameterized Build):**
   - Check **This project is parameterized** $\rightarrow$ Add **Choice Parameter**.
   - **Name:** `SCRIPT_NAME`
   - **Choices:**
     ```text
     test:regression
     test:web
     test:api
     test:smoke
     ```
4. **Configure Build Command Step:**
   - Add Build Step $\rightarrow$ **Execute shell** (macOS/Linux) or **Execute Windows batch command** (Windows).
   - **Shell Command (macOS/Linux):**  
     `npm run "${SCRIPT_NAME}"`
   - **Batch Command (Windows):**  
     `npm run "%SCRIPT_NAME%"`

---

## Key Characteristics

- **Parameterized Execution:** Allows QA teams to select specific test targets (`test:web`, `test:api`) from a Jenkins dropdown UI without editing code.
- **Automated Failure Reporting:** Fails the Jenkins build step if any Playwright test fails, preventing bad builds from deploying.
- **Headless Execution:** Jenkins agents execute tests automatically in headless mode (`headless: true`).

---

## Common Mistakes

- **Using Unix variable syntax inside Windows Batch steps:** Writing `$SCRIPT_NAME` inside a Windows Batch command instead of `%SCRIPT_NAME%`. (Unix/macOS uses `${SCRIPT_NAME}`; Windows Batch uses `%SCRIPT_NAME%`).
- **Forgetting to install Playwright browser binaries on CI agents:** Running tests on a fresh Jenkins worker without executing `npx playwright install --with-deps` first.

---

## Canonical Jenkins Configuration Summary

### Unix/macOS Execute Shell Step

```bash
#!/bin/bash
# Jenkins Build Execution Step for macOS/Linux agents
echo "Executing Playwright test target: ${SCRIPT_NAME}"

# Install dependencies if needed
npm install

# Run parameterized npm script
npm run "${SCRIPT_NAME}"
```

### Windows Batch Command Step

```cmd
:: Jenkins Build Execution Step for Windows agents
echo Executing Playwright test target: %SCRIPT_NAME%

:: Run parameterized npm script
npm run %SCRIPT_NAME%
```

---

## Key Takeaways

- Create a Jenkins Freestyle job pointing to your Git repository or local workspace.
- Parameterize builds using Choice Parameters (`SCRIPT_NAME`) to allow execution selection from a UI dropdown.
- Execute tests via shell/batch steps using `npm run "${SCRIPT_NAME}"`.

---

## Related

- [[node-custom-npm-scripts-automation]] — Defining npm scripts
- [[pw-test-tagging-selective-execution]] — Tagging and CLI options
- [[pw-allure-reporting-integration]] — Publishing reports in CI
- [[MOC - Playwright Framework Design]]
