---
id: 202608152031
title: Playwright Interactive UI Runner with npx playwright test --ui
aliases:
  - playwright ui runner
  - ui mode
  - test --ui
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/execution
date_created: 2026-08-15
mastery_level: 1
---

# Playwright Interactive UI Runner with npx playwright test --ui

---

> **TL;DR:** `npx playwright test --ui` opens Playwright UI Mode—an interactive desktop test dashboard that combines live test execution, visual DOM snapshot timelines, watch mode, and interactive locator exploration in one unified application.

---

## Why This Exists

Switching back and forth between terminal commands, headless test runs, trace viewer zip archives, and browser inspector tools slows down test development. Playwright provides **UI Mode** (`npx playwright test --ui`), bringing together test discovery, execution, time-travel debugging, watch mode, and selector exploration into an interactive visual dashboard.

---

## Mental Model

Imagine an all-in-one music production workbench. Instead of using separate devices to record, edit, mix, and playback tracks in isolation, UI Mode puts the keyboard, multi-track timeline, visual waveform display, and live monitoring headphones directly in front of you on a single console.

---

## How It Works

1. **Launch UI Mode:** Execute `npx playwright test --ui` in terminal.
2. **Interactive Workspace Interface:**
   - **Test Suite Explorer (Left Pane):** Lists all spec files and individual `test()` items found in `testDir`.
   - **Interactive Viewport (Center Pane):** Live browser canvas displaying before/after action snapshots.
   - **Time-Travel Action Timeline (Bottom Pane):** Step-by-step execution log with millisecond timing breakdowns.
   - **Watch Mode Toggle (Eye Icon):** Automatically re-runs tests whenever spec files are edited and saved in your IDE.

---

## Key Characteristics

- **Built-in Watch Mode:** Watches spec code changes and re-executes targeting tests instantly upon save.
- **Interactive Action Hover:** Hover over any historical step to see the browser canvas render exact before/after DOM states.
- **Visual Locator Picker:** Pick elements directly off the live UI canvas to inspect optimal selector strings.

---

## Common Mistakes

- **Running UI Mode on headless CI servers:** Attempting `npx playwright test --ui` in GitHub Actions or Jenkins build pipelines. UI Mode requires a graphical desktop display!
- **Leaving `--ui` flags in CI script definitions:** Placing `npx playwright test --ui` inside `package.json` build scripts meant for automated CI pipelines.

---

## Canonical Command Usage

```bash
# Launch Playwright UI Mode dashboard
npx playwright test --ui

# Launch UI Mode targeting a specific test spec file
npx playwright test tests/login.spec.js --ui
```

---

## Key Takeaways

- `npx playwright test --ui` launches the visual Playwright UI Mode dashboard.
- Combines test execution, time-travel DOM snapshots, watch mode, and selector picking into one window.
- Designed strictly for local test development and debugging (do not use in CI).

---

## Related

- [[pw-step-debugging-inspector]] — Interactive Playwright Inspector
- [[pw-cli-execution-flags]] — Command-line flags reference
- [[MOC - Playwright Execution]]
