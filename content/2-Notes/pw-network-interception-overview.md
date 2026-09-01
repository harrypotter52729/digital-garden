---
id: 202608151954
title: Playwright Network Interception and Mocking Overview
aliases:
  - network interception
  - api mocking
  - playwright route
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/network
date_created: 2026-08-15
mastery_level: 1
---

# Playwright Network Interception and Mocking Overview

---

> **TL;DR:** Network interception is like an inline postal inspection station that lets you intercept outgoing browser network calls, alter request payloads, swap response data, or block requests entirely without touching backend servers.

---

## Why This Exists

Testing edge cases (such as 500 server errors, slow 3G connections, empty data lists, or unreleased backend API features) directly against live staging backends is unreliable, slow, and expensive. Playwright provides built-in network interception capabilities using `page.route()` to modify network traffic seamlessly.

---

## Mental Model

Imagine a web browser as a client sending sealed letters (HTTP requests) to a postal office (backend server). Playwright acts as an inline proxy officer: before a letter leaves the browser process, Playwright inspects the destination URL. It can open the letter, alter the contents, construct a synthetic response letter on the spot, or throw the letter away (abort request).

---

## How It Works

1. **Route Handler Registration:** Define a route pattern matching target URLs using `page.route(urlPattern, handler)`.
2. **Request Trapping:** Whenever the browser initiates an HTTP/HTTPS fetch or XHR matching `urlPattern`, execution is paused and passed to the route handler.
3. **Fulfill / Continue / Abort Action:**
   - `route.fulfill()`: Returns mock response data (JSON, HTML, status code).
   - `route.continue()`: Passes request to network uninterrupted (can modify headers).
   - `route.abort()`: Fails request instantly (simulates offline or network error).

---

## Key Characteristics

- **Zero Mock Server Setup:** Operates natively inside the browser context without external proxy binaries.
- **Support for Glob & Regex Matching:** Match single endpoints or broad API pattern scopes.
- **Edge Case Simulation:** Test server crashes (500), unauthorized access (401/403), or network dropouts easily.

---

## Common Mistakes

- **Not awaiting `page.route()` registration before triggering navigation:** Register routes *before* `page.goto()` to catch initial page API requests.
- **Forgetting `route.continue()` or `route.fulfill()`:** Leaving a route handler hanging will block browser execution indefinitely.

---

## Best Practices

- Register network routes at the beginning of tests before page navigation.
- Store mock JSON payloads in clean fixture files rather than hardcoding large inline JSON blobs.

---

## Canonical Code Example

```javascript
// Demonstrating Playwright page.route() network interception
const { test, expect } = require('@playwright/test');

test('mock API response for user profile', async ({ page }) => {
  // Intercept GET requests matching the API endpoint BEFORE navigating
  await page.route('**/api/v1/user/profile', async (route) => {
    // Fulfill request with mock JSON payload
    await route.fulfill({
      status: 200,
      contentType: 'application/json',
      body: JSON.stringify({
        id: 42,
        name: 'Jane Doe',
        role: 'Administrator',
      }),
    });
  });

  // Navigate to application page
  await page.goto('https://example.com/dashboard');

  // UI renders mocked profile data without contacting backend server
  await expect(page.locator('#user-name')).toHaveText('Jane Doe');
  await expect(page.locator('#user-role')).toHaveText('Administrator');
});
```

---

## Key Takeaways

- `page.route()` intercepts, modifies, and mocks browser network calls natively.
- Enables rapid testing of edge cases (500 errors, network dropouts, empty datasets).
- Must be declared prior to triggering network calls.

---

## Related

- [[pw-architecture-and-features]] — Direct CDP protocol integration
- [[pw-browser-context-storage]] — Context state management
- [[MOC - Playwright Locators and Actions]]
