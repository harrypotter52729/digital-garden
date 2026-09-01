---
id: 202608152049
title: Network Request Aborting and Event Telemetry
aliases:
  - route.abort
  - blocking assets
  - page.on request
  - page.on response
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/network
date_created: 2026-08-15
mastery_level: 1
---

# Network Request Aborting and Event Telemetry

---

> **TL;DR:** `route.abort()` blocks unwanted network requests (like images, fonts, or tracking scripts) to speed up test execution, while `page.on('request')` and `page.on('response')` log live network telemetry for debugging.

---

## Why This Exists

Loading heavy media assets (high-resolution PNG/JPEG images, custom web fonts, third-party analytics scripts) slows down automated test suite execution. Playwright provides `route.abort()`, allowing tests to block specific resource types on the fly. Additionally, event listeners (`page.on('request')`, `page.on('response')`) monitor live network traffic for status code errors (`404`, `500`).

---

## Mental Model

Imagine driving on an automated toll highway.
- **Resource Aborting (`route.abort()`):** Setting up road barriers that divert heavy semi-trucks (large `.jpg` images and `.css` animations) onto detour routes while allowing fast sports cars (essential JS code and REST APIs) to zoom through at 100 mph.
- **Event Telemetry (`page.on('response')`):** A highway traffic camera recording every car license plate (`request URL`) and pass status (`status code 200`) as vehicles zip through toll booths.

---

## How It Works

### Resource Aborting with `route.abort()`
1. **Match Asset Patterns:** Register route handler targeting specific file extensions:
   ```javascript
   await page.route('**/*.{png,jpg,jpeg,css}', (route) => route.abort());
   ```
2. **Execution Effect:** Browsers block matching image/CSS network calls instantly, speeding up page load times.

### Network Event Telemetry
- **Request Listener:**  
  `page.on('request', req => console.log(`REQ: ${req.url()}`));`
- **Response Listener:**  
  `page.on('response', res => console.log(`RES [${res.status()}]: ${res.url()}`));`

---

## Key Characteristics

- **Massive Performance Boost:** Aborting images and heavy CSS stylesheets speeds up page loads by 30-50%.
- **Live Status Code Monitoring:** Captures non-200 HTTP response status codes (`400`, `500`) automatically during test runs.
- **Flexible Pattern Matching:** Block by extension (`*.png`), domain (`*analytics.google.com*`), or resource type (`image`, `stylesheet`, `font`).

---

## Common Mistakes

- **Aborting CSS stylesheets on layout-dependent tests:** Aborting `.css` files on tests that rely on element visibility or layout positions. Aborting CSS may cause elements to become hidden or un-clickable!
- **Placing `page.on()` event listeners after navigation:** Attaching network event listeners *after* calling `page.goto()`. Initial page loading requests are missed!

---

## Canonical Code Example

```javascript
// Aborting media assets and logging network telemetry in Playwright
const { test, expect } = require('@playwright/test');

test('abort image requests and log network response status codes', async ({ page }) => {
  // 1. Attach network telemetry event listeners BEFORE navigation
  page.on('request', (req) => console.log(`--> ${req.method()} ${req.url()}`));
  page.on('response', (res) => {
    if (res.status() >= 400) {
      console.error(`❌ FAILED REQUEST [${res.status()}]: ${res.url()}`);
    }
  });

  // 2. Abort all image resource requests to accelerate page load
  await page.route('**/*.{png,jpg,jpeg,svg}', (route) => route.abort());

  // 3. Navigate to target page (loads fast without images!)
  await page.goto('https://example.com/shop');

  // Verify page functional integrity
  await expect(page.locator('h1')).toBeVisible();
});
```

---

## Key Takeaways

- Use `route.abort()` to block heavy media files (`.png`, `.jpg`, `.woff2`) and speed up test runs.
- Use `page.on('request')` and `page.on('response')` to log live network traffic and detect HTTP errors (`404`, `500`).
- Be cautious when aborting CSS files, as unstyled layouts can break element visibility checks.

---

## Related

- [[pw-network-response-interception-mocking]] — Response mocking
- [[pw-network-request-modification]] — Request modification
- [[MOC - Playwright Network Interception]]
