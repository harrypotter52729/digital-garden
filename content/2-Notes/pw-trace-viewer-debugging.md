---
id: 202608151953
title: Playwright Trace Viewer and Time Travel Debugging
aliases:
  - trace viewer
  - time travel debugging
  - playwright tracing
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/debugging
date_created: 2026-08-15
mastery_level: 1
---

# Playwright Trace Viewer and Time Travel Debugging

---

> **TL;DR:** Trace Viewer is like a high-definition DVR security camera system for your test execution, allowing you to rewind, fast-forward, inspect DOM snapshots, review network payloads, and pinpoint failure lines step-by-step.

---

## Why This Exists

When automated tests fail in headless continuous integration (CI) environments, static screenshots and terminal stack traces often lack sufficient context to diagnose root causes (e.g., race conditions, intermediate network failures, DOM state mismatches). Playwright Trace Viewer records complete execution telemetry so developers can debug CI test failures locally with full visual and diagnostic context.

---

## Mental Model

Imagine a flight data recorder (black box) combined with interactive time travel. Instead of seeing only the final crash site (a single failure screenshot), Trace Viewer records every control surface input, engine telemetry (network calls), and visual viewport frame. You can step forward and backward in time, inspecting the live DOM state at any exact microsecond.

---

## How It Works

1. **Telemetry Collection:** During test execution, Playwright captures DOM snapshots (before, during, and after each action), browser console logs, network requests/responses, and source code call stacks.
2. **Zip Archive Bundle:** Traces are saved into a compact `.zip` file archive.
3. **PWA Inspection UI:** The trace file is loaded in Playwright's standalone Web UI (`npx playwright show-trace trace.zip`), rendering an interactive timeline with side-by-side DOM inspection tabs.

---

## Key Characteristics

- **Action Timeline:** Visual strip showing browser frames over time.
- **Before / Action / After Snapshots:** Live interactive DOM render for every step.
- **Network Tab:** Full HTTP request headers, body, response status, and timing timeline.
- **Console & Source Tab:** Live link to exact test code file and line number.

---

## Common Mistakes

- **Enabling tracing for every passing local test:** Tracing adds disk storage overhead. It should be configured to run on `on-first-retry` or `retain-on-failure` in CI.
- **Trying to open `trace.zip` directly as a regular zip file:** The zip archive must be opened using `npx playwright show-trace <path-to-trace.zip>`.

---

## Configuration Options

```javascript
// playwright.config.js
module.exports = {
  use: {
    // Tracing mode options: 'off', 'on', 'retain-on-failure', 'on-first-retry'
    trace: 'on-first-retry',
  },
};
```

---

## Canonical Code Example

```javascript
// Demonstrating programmatic trace capture setup
const { test, expect } = require('@playwright/test');

test.describe('Checkout Flow', () => {
  test('user authentication and order placement', async ({ page, context }) => {
    // Programmatically start tracing for complex multi-context flows
    await context.tracing.start({ screenshots: true, snapshots: true, sources: true });

    await page.goto('https://example.com/login');
    await page.fill('#username', 'testuser');
    await page.fill('#password', 'secretpass');
    await page.click('#login-button');

    await expect(page.locator('.welcome-banner')).toBeVisible();

    // Stop tracing and save zip archive
    await context.tracing.stop({ path: 'traces/checkout-failure-trace.zip' });
  });
});
```

---

## Key Takeaways

- Trace Viewer provides post-mortem time-travel debugging for headless test execution.
- Captures before/after DOM snapshots, network payloads, console logs, and source lines.
- Best configured in CI with `trace: 'on-first-retry'` to optimize storage and speed.

---

## Related

- [[pw-architecture-and-features]] — Platform overview
- [[pw-configuration-file]] — Setting trace properties in config
- [[MOC - Playwright Execution]]
