---
id: 202608152003
title: Playwright Test Fixtures and Dependency Injection
aliases:
  - test fixtures
  - page fixture
  - context fixture
  - dependency injection
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/architecture
date_created: 2026-08-15
mastery_level: 1
---

# Playwright Test Fixtures and Dependency Injection

---

> **TL;DR:** Fixtures are like pre-set room service trays delivered automatically to your room—instead of cooking breakfast yourself (setting up browser, context, and page instances), you simply declare `{ page }` in your function signature, and Playwright delivers a fresh tab ready to use.

---

## Why This Exists

Manual setup and teardown boilerplate code (`beforeEach` and `afterEach` hooks for instantiating browsers, opening pages, and closing context instances) clutters test suites and leads to resource leakage when cleanup steps fail. Playwright uses a modular **Dependency Injection Fixture System** to manage setup, isolated execution environment delivery, and automatic teardown.

---

## Mental Model

Imagine ordering a rental car for a road trip.
- **Without Fixtures:** You build the engine, attach wheels, fill gas, drive, wash the car, and disassemble the engine when finished.
- **With Fixtures (`{ page }`):** You request a key (`async ({ page }) => ...`). The rental agency hands you a clean, fully fueled car (`page`). When you finish your trip, the agency collects the car and handles cleaning and recycling (`automatic teardown`) behind the scenes.

---

## How It Works

1. **Fixture Declaration:** In your test callback, request desired built-in fixtures using ES6 destructuring parameter syntax:  
   `test('name', async ({ page, context, request }) => { ... });`
2. **On-Demand Instantiation:** Playwright inspects requested parameter keys and instantiates only the necessary objects.
3. **Automatic Teardown:** Once the test callback completes (or throws an error), Playwright automatically executes teardown logic (closing contexts, saving traces, closing pages).

---

## Built-In Playwright Fixtures

| Fixture Name | Type | Scope | Description |
| :--- | :--- | :--- | :--- |
| `page` | `Page` | Test | Isolated browser tab for UI interaction. |
| `context` | `BrowserContext` | Test | Isolated session context. |
| `browser` | `Browser` | Worker | Shared browser engine instance across tests in a worker. |
| `request` | `APIRequestContext` | Test | Isolated API client for network/REST calls. |

---

## Key Characteristics

- **Lazy Evaluation:** Fixtures are initialized only if explicitly requested in the test signature.
- **Automatic Isolation:** Every test requesting `{ page }` receives a completely isolated `BrowserContext` and `Page`.
- **Custom Extensibility:** Developers can extend base `test` objects to inject custom fixtures (e.g., pre-built Page Object Models or custom test database clients).

---

## Common Mistakes

- **Forgetting destructured curly braces `{}` in test signature:** Writing `async (page) => { ... }` instead of `async ({ page }) => { ... }`. Omitting `{}` passes the entire test runner context object instead of the `page` fixture.
- **Manually calling `browser.close()` inside a fixture-based test:** Playwright manages fixture lifecycles automatically; closing the browser manually disrupts test runner cleanup.

---

## Canonical Code Example

```javascript
// Demonstrating Playwright built-in test fixtures
const { test, expect } = require('@playwright/test');

// Playwright automatically injects isolated 'page' and 'request' fixtures
test('verify dashboard UI and backend API status', async ({ page, request }) => {

  // 1. Using 'request' API fixture to query backend REST endpoint directly
  const apiResponse = await request.get('https://example.com/api/v1/health');
  expect(apiResponse.ok()).toBeTruthy();

  // 2. Using 'page' UI fixture to test front-end interface
  await page.goto('https://example.com/dashboard');
  await expect(page.locator('.status-badge')).toHaveText('Operational');

  // No manual browser/page teardown needed! Playwright recycles resources automatically.
});
```

---

## Key Takeaways

- Fixtures eliminate setup/teardown boilerplate via dependency injection.
- Request fixtures using destructured object syntax: `{ page, context }`.
- Playwright manages fixture lifecycles and guarantees test isolation.

---

## Related

- [[pw-browser-context-page-hierarchy]] — Hierarchy of underlying fixture objects
- [[pw-test-annotation-structure]] — Spec file layout
- [[MOC - Playwright Architecture]]
