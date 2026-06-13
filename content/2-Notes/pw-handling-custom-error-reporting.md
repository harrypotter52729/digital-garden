---
id: 202606131246
title: Handling Custom Error Reporting
aliases:
  - playwright-error-logs
  - test-failures
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/assertions
date_created: 2026-06-13
mastery_level: 1
---

# Handling Custom Error Reporting

> **TL;DR:** When automated tests fail in CI/CD, you need unified log diagnostic folders. Playwright reports test script failures automatically, but browser console crashes and uncaught page exceptions (`TypeError: cannot read property of undefined`) are swallowed silently by default. You must capture these client-side errors using the `pageerror` event emitter.

## The Flight Crash Analogy

Imagine analyzing a flight accident after a crash:

```
Test Script Failure (Hard Assertion fail):
"Failed: Button not found."
- This tells you the destination was never reached, but doesn't explain if the engine failed.

Page Console Crash (Swallowed Exception):
"Uncaught ReferenceError: profileData is not defined."
- The browser crashed inside the engine room, halting JavaScript execution, but the test runner just timed out waiting for the login indicator, leaving you in the dark.
```

By hooking into Playwright's **Client Error Listeners**, you merge the **Cockpit Voice Recorder** (browser console events) with the **Air Traffic Control Logs** (your test execution script) to get a single, clear diagnostics report.

---

## Capturing Client-side vs. Server-side Errors

- **Test Script Errors:** Captured by standard JS `try-catch` structures inside your testing framework or flagged by failing `expect()` assertions.
- **Client-side Page Errors:** Triggered when the browser page encounters an unhandled JavaScript error or uncaught promise rejection. These are caught in Playwright using `page.on('pageerror', handler)`.

---

## Canonical Code Example

This code registers client-side page error listeners to fail tests immediately if the browser encounters a console script crash, and shows how to wrap hooks in try-catch-finally blocks to output custom stack logs.

```javascript
// @ts-check
const { test, expect } = require("@playwright/test");

test.describe("Client Console Error Auditor", () => {
  let browserErrors = [];

  // Register listener before each test to capture client-side exceptions
  test.beforeEach(async ({ page }) => {
    browserErrors = [];
    
    // Catch uncaught browser JS errors
    page.on("pageerror", (exception) => {
      console.error(`>> CLIENT ERROR CAUGHT: ${exception.message}`);
      browserErrors.push(exception);
    });
  });

  // Fail the test if any console error was generated during run
  test.afterEach(async () => {
    if (browserErrors.length > 0) {
      const errorList = browserErrors.map((err) => err.stack || err.message).join("\n");
      throw new Error(`Test failed due to client-side page crashes:\n${errorList}`);
    }
  });

  test("Verify page loads without console errors", async ({ page }) => {
    // If this page triggers an uncaught JS script exception in the header,
    // the pageerror listener will record it, and the test will fail in afterEach.
    await page.goto("/dashboard");
    
    const header = page.locator("h1");
    await expect(header).toBeVisible();
  });

  test("Handling custom throw capture in test scripts", async ({ page }) => {
    await page.goto("/catalog");

    try {
      // Intentional script logic validation wrapper
      const checkoutBtn = page.locator("#checkout-disabled");
      await checkoutBtn.click({ timeout: 2000 }); // Short timeout for check
    } catch (error) {
      // Re-throw a custom, structured error with helpful debugging details
      throw new Error(`[Checkout Flow Failure]: Could not click button. Root cause:\n${error.message}`);
    }
  });
});
```

---

## Related
* [[js-throwing-custom-errors]] - Creating custom, semantic JavaScript error instances.
* [[js-try-catch-finally-flow]] - Customizing execution block wraps to clean resources on exceptions.
* [[pw-html-reporter-and-traces]] - Visualizing script trace errors inside the HTML Reporter layout.
* [[js-try-catch-finally-flow]] - Utilizes try-catch-finally blocks to safely clear resources when test errors occur.
* [[js-throwing-custom-errors]] - Translates browser exceptions and runner failures into custom Node.js error objects.
