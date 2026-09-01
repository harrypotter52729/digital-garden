---
id: 202608152020
title: Step-by-Step Interactive Debugging with Playwright Inspector
aliases:
  - playwright inspector debugging
  - debug flag
  - page.pause debugging
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/debugging
date_created: 2026-08-15
mastery_level: 1
---

# Step-by-Step Interactive Debugging with Playwright Inspector

---

> **TL;DR:** Playwright Inspector is an interactive debugging cockpit that pops open alongside your browser—allowing you to step through test lines one by one, highlight selector targets visually before actions occur, and test new selectors live.

---

## Why This Exists

Headless automated test execution runs in milliseconds. When a locator fails or an unexpected popup obscures a button, identifying the failure point from terminal logs alone is difficult. Playwright Inspector allows developers to step line-by-line through test code, inspecting DOM targets and action logs in real time.

---

## Mental Model

Imagine an inspector stepping onto a manufacturing assembly line. Instead of letting the conveyor belt zip past at 100 miles per hour, the inspector presses the yellow lever (`--debug`). The conveyor belt pauses at each station. Before a robotic arm attaches a part (`click()`), the arm highlights the target location in red, allowing the inspector to verify precision before pressing "Next Step".

---

## How It Works

1. **Activation Trigger:**
   - **CLI Flag:** Run `npx playwright test --debug` (or `npx playwright test spec.js --debug`).
   - **Code Pause:** Insert `await page.pause()` directly into test spec code.
2. **Inspector Window Controls:**
   - **Step Over (F10):** Executes current line and pauses at the next action line.
   - **Resume (F8):** Resumes execution at full speed until the next breakpoint or test completion.
   - **Explore Tool:** Click any element in the browser window to automatically generate its optimal Playwright CSS/Role locator.
   - **Console Log Stream:** Displays detailed pre-action actionability checks (visibility, stability, event listener attachment).

---

## Key Characteristics

- **Pre-Action Highlight:** Highlights target elements in red on the browser canvas *before* performing clicks or inputs.
- **Actionability Log Stream:** Shows step-by-step checks (`waiting for element to be visible... element is visible... attempting click`).
- **Live Selector Evaluator:** Type custom CSS or XPath selectors into the Inspector search box to test matching counts live.

---

## Common Mistakes

- **Running `--debug` on multi-worker CI environments:** Debug mode is designed for single-worker local execution.
- **Forgetting that non-action lines are skipped:** Lines that merely declare locators (`const btn = page.locator('#btn')`) are not action steps; Inspector pauses only when action methods (`click()`, `fill()`) are called.

---

## Canonical Code Example

```javascript
// Demonstrating Playwright Inspector step debugging
const { test, expect } = require('@playwright/test');

test('debug complex form submission step by step', async ({ page }) => {
  await page.goto('https://example.com/login');

  // Insert explicit breakpoint to open Playwright Inspector GUI
  // (Alternatively run: npx playwright test --debug)
  await page.pause();

  await page.fill('#username', 'admin_user');
  await page.fill('#password', 'secret_password');
  await page.click('#submit-btn');

  await expect(page.locator('.dashboard-header')).toBeVisible();
});
```

---

## Key Takeaways

- Trigger Inspector via `npx playwright test --debug` or `await page.pause()`.
- Provides step-over execution, live selector exploration, and pre-action element highlighting.
- Displays actionability log streams explaining why actions wait or fail.

---

## Related

- [[pw-trace-viewer-debugging]] — Post-mortem trace viewer inspection
- [[pw-cli-execution-flags]] — Command-line debug flags
- [[MOC - Playwright Execution]]
