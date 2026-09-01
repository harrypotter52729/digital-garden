---
id: 202608152044
title: VS Code Interactive Debugging for Hybrid API-UI Specs
aliases:
  - vscode debugging
  - debug npm script
  - hybrid api-ui debugging
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/debugging
date_created: 2026-08-15
mastery_level: 1
---

# VS Code Interactive Debugging for Hybrid API-UI Specs

---

> **TL;DR:** While `npx playwright test --debug` only steps through browser UI actions, triggering VS Code's "Debug npm Script" command allows developers to place breakpoints inside both API utility helper classes (`APIUtils.js`) and UI test specs.

---

## Why This Exists

Playwright Inspector (`--debug`) is optimized for visual browser UI stepping. However, when test scripts combine REST API data pre-conditioning (`request.post()`) with browser UI automation, Playwright Inspector completely skips non-UI JavaScript API logic in `beforeAll` hooks. To inspect API request payloads, debug token parsing, or step through `APIUtils` helper methods, developers use VS Code's native Node.js debugger.

---

## Mental Model

Imagine inspecting an automated bank ATM.
- **Playwright Inspector (`--debug`):** Watching the mechanical card slot and touch screen on the outside of the machine. It can't see the internal bank network API request sent to authorize the withdrawal.
- **VS Code Debugger ("Debug npm Script"):** Putting on night-vision goggles that let you inspect both the mechanical touchscreen on the outside AND the internal computer microchip processing network API calls on the inside simultaneously.

---

## How It Works

1. **Configure `package.json` script entry:**
   ```json
   "scripts": {
     "test:api": "playwright test tests/webApi.spec.js"
   }
   ```
2. **Increase Config Timeout:** Set `timeout: 120000` inside `playwright.config.js` to prevent 30-second test timeouts while inspecting variables.
3. **Launch VS Code Node Debugger:**
   - Press `Ctrl + Shift + P` (or `Cmd + Shift + P` on macOS).
   - Type **"Debug: Debug npm Script"** and press Enter.
   - Select `"test:api"`.
4. **Interactive Inspection:** Hover over variables (`token`, `orderResponse`) or use the VS Code Debug Console to evaluate live expressions.

---

## Key Characteristics

- **Full Execution Stepping:** Steps through Node.js API logic, `APIUtils` helper classes, and browser UI steps seamlessly.
- **Live Variable Hovering:** Hover over any variable in VS Code to inspect nested JSON response objects.
- **Bypasses Playwright Inspector Limitations:** Debugs non-browser JavaScript code inside `test.beforeAll()` hooks.

---

## Common Mistakes

- **Forgetting to increase `playwright.config.js` timeout:** Pausing on a breakpoint for more than 30 seconds cause Playwright's test runner to throw a `Test timeout of 30000ms exceeded` failure. Always increase test timeout while debugging.
- **Relying on `console.log()` everywhere instead of breakpoints:** Scattering dozens of `console.log()` statements throughout API utility code instead of using VS Code breakpoints.

---

## Canonical Setup Configuration

### `package.json`

```json
{
  "name": "playwright-automation-suite",
  "scripts": {
    "test:web-api": "playwright test tests/webApi.spec.js --headed"
  }
}
```

### VS Code Debugger Command Execution

```text
1. Press Ctrl+Shift+P (Cmd+Shift+P on Mac)
2. Select: > Debug: Debug npm Script
3. Click: test:web-api
```

---

## Key Takeaways

- Use VS Code "Debug npm Script" to debug hybrid API and UI test code simultaneously.
- Enables stepping through `beforeAll` API setups and `APIUtils` class methods.
- Increase test timeout in `playwright.config.js` to prevent timeout failures while debugging.

---

## Related

- [[pw-step-debugging-inspector]] — Playwright Inspector UI debugging
- [[pw-trace-viewer-api-payloads]] — Trace Viewer API inspection
- [[MOC - Playwright Execution]]
