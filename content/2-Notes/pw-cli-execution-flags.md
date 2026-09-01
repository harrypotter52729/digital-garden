---
id: 202608152005
title: Playwright CLI Command Line Execution Flags
aliases:
  - npx playwright test flags
  - playwright cli options
  - running playwright tests
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/execution
date_created: 2026-08-15
mastery_level: 1
---

# Playwright CLI Command Line Execution Flags

---

> **TL;DR:** `npx playwright test` flags are like remote control buttons for your test suite—allowing you to dynamically target specific test files, switch from headless to headed mode, run individual browser projects, or debug step-by-step without editing source code.

---

## Why This Exists

Hardcoding temporary changes (such as making a test run in headed mode, running only a single test file, or enabling debug mode) directly into `playwright.config.js` risks accidentally committing debug code into repository version control. Playwright provides command-line interface (CLI) flags to override configuration settings dynamically at execution runtime.

---

## Mental Model

Imagine your test framework as a car equipped with standard default cruise control settings (`playwright.config.js`). CLI flags act as manual dashboard toggle buttons: pressing `--headed` flips open the sunroof (visible browser), passing `--project=firefox` switches engine modes, and passing `--ui` opens an interactive visual dashboard.

---

## How It Works

`npx playwright test` resolves the local Playwright CLI binary inside `node_modules/` via NPX, parses user-provided flags, merges them over defaults defined in `playwright.config.js`, and executes matching test files.

---

## Essential CLI Execution Flags

| Flag Syntax | Purpose | Description |
| :--- | :--- | :--- |
| `npx playwright test` | Default Run | Executes all spec files in headless mode across all projects. |
| `--headed` | Visible Browser | Launches visible browser windows instead of headless execution. |
| `--project=<name>` | Target Project | Restricts execution to a specific project (e.g., `--project=chromium`). |
| `<filename>` | Target File | Runs only specified spec file (e.g., `tests/login.spec.js`). |
| `-g "<pattern>"` | Target Title | Runs tests matching title regex pattern (e.g., `-g "login"`). |
| `--ui` | Interactive UI | Launches Playwright visual UI Mode runner. |
| `--debug` | Playwright Inspector | Pauses execution and opens interactive inspector debugger. |
| `--workers=<n>` | Worker Count | Overrides parallel worker thread count (e.g., `--workers=1`). |
| `--retries=<n>` | Retry Count | Sets custom retry count for flaky test detection. |

---

## Common Mistakes

- **Forgetting `npx` prefix:** Running `playwright test` directly in terminals where Playwright is not installed globally raises `command not found`.
- **Misspelling project names:** Passing `--project=chrome` instead of the project name declared in `playwright.config.js` (e.g., `--project="Desktop Chromium"`).

---

## Practical Command Workflows

```bash
# 1. Run a specific spec file in headed mode for local debugging
npx playwright test tests/ui-basics.spec.js --headed

# 2. Run only tests matching a specific title tag or name
npx playwright test -g "@smoke"

# 3. Run tests exclusively on Firefox engine
npx playwright test --project=firefox

# 4. Debug a failing test step-by-step using Playwright Inspector
npx playwright test tests/ui-basics.spec.js --debug

# 5. Launch interactive visual Playwright UI mode
npx playwright test --ui

# 6. View generated HTML test report after execution
npx playwright show-report
```

---

## Key Takeaways

- CLI flags override `playwright.config.js` options dynamically without altering code files.
- `npx playwright test --headed` enables visual browser window execution.
- `--ui` and `--debug` provide interactive debugging tools for rapid test development.

---

## Related

- [[pw-configuration-file]] — Central config properties overridden by CLI
- [[pw-trace-viewer-debugging]] — Inspecting execution traces
- [[pw-test-annotation-structure]] — Filtering tests by title or annotation
- [[MOC - Playwright Execution]]
