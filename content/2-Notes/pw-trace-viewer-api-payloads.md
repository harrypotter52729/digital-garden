---
id: 202608152045
title: Inspecting API Network Payloads in Playwright Trace Viewer
aliases:
  - trace viewer api
  - network payload inspection
  - api tracing
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/debugging
date_created: 2026-08-15
mastery_level: 1
---

# Inspecting API Network Payloads in Playwright Trace Viewer

---

> **TL;DR:** Playwright Trace Viewer automatically records both browser UI actions AND background REST API request/response payloads in `trace.zip` archives—allowing developers to inspect API status codes, request bodies, and JSON responses without extra logging code.

---

## Why This Exists

When hybrid API-UI tests fail on CI build agents, determining whether the failure was caused by a broken REST API endpoint (e.g., `500 Internal Server Error`) or a missing UI element is difficult from terminal logs alone. Playwright Trace Viewer captures all API network calls made via `request` contexts or browser fetch requests, presenting request headers, POST payloads, and JSON response bodies in an interactive visual inspector.

---

## Mental Model

Imagine a black box flight recorder on an airplane.
It doesn't just record high-definition video of the cockpit controls (`browser UI snapshot timeline`). It simultaneously records every single radio transmission and digital telemetry signal sent between the cockpit and air traffic control (`API REST request/response payloads`).

---

## How It Works

1. **Enable Trace Recording:** Configure `trace: 'on'` or `trace: 'retain-on-failure'` inside `playwright.config.js`.
2. **Execute Test Suite:** Run tests via `npx playwright test`.
3. **Open Trace Archive:** Launch the visual trace viewer:
   `npx playwright show-trace test-results/path-to-trace.zip`
4. **Network & Call Tab Inspection:**
   - **Call Tab:** Displays `apiRequestContext.post` call parameters, request data payloads, and response status codes (`200 OK`).
   - **Network Tab:** Displays timing graphs, HTTP headers, request JSON bodies, and response JSON payloads.

---

## Key Characteristics

- **Zero Custom Logging Code:** Eliminates the need to scatter `console.log(responseJson)` statements across test code.
- **Combined API and UI Timeline:** Correlates API request completion times directly against UI page load events.
- **Post-Mortem CI Artifact Inspection:** Download `trace.zip` from failed CI runs to inspect API payloads locally.

---

## Common Mistakes

- **Assuming Trace Viewer only records browser UI clicks:** Forgetting that API context requests (`request.post()`) executed in `beforeAll` hooks are also recorded inside `trace.zip`.
- **Forgetting `trace: 'on'` during local debugging:** Expecting `trace.zip` to generate when trace configuration is set to `'off'`.

---

## Canonical Config & Inspection Commands

```javascript
// playwright.config.js trace configuration
const { defineConfig } = require('@playwright/test');

module.exports = defineConfig({
  use: {
    // Record trace archives for all tests to capture API payloads
    trace: 'on',
  },
});
```

```bash
# Command to view generated trace archive
npx playwright show-trace test-results/webApi-test-spec/trace.zip
```

---

## Key Takeaways

- Trace Viewer records REST API request payloads, headers, and JSON responses automatically.
- Inspect API logs inside the **Call** and **Network** tabs of `trace.zip`.
- Essential for post-mortem debugging of failed hybrid API-UI tests in CI.

---

## Related

- [[pw-trace-viewer-debugging]] — Trace Viewer overview
- [[pw-vscode-hybrid-debugging]] — VS Code debugging
- [[MOC - Playwright Execution]]
