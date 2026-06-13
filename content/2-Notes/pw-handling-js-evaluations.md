---
id: 202606131244
title: Handling JS Evaluations
aliases:
  - page-evaluate
  - browser-execution-context
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/execution-cicd
date_created: 2026-06-13
mastery_level: 1
---

# Handling JS Evaluations

> **TL;DR:** Playwright test scripts run in a Node.js process, which is completely separate from the browser's JavaScript execution engine. To read browser-native variables (like `window.scrollY`) or call custom client-side functions, you must use `page.evaluate()` or `locator.evaluate()` to serialize and execute instructions inside the browser window.

## The Remote Clerk Analogy

Imagine you are a supervisor sitting at **Company Headquarters** (Node.js runner process), and you need to inspect a shelf inside a **Remote Warehouse** (Browser page context):

```
[ Node.js Test Process ]                         [ Browser Page Context ]
(Company Headquarters)                             (Remote Warehouse)
          |                                                 |
          | ---- 1. Send instruction letter (serialize) --> |
          |                                                 | -- 2. Read letter & execute --+
          |                                                 |    (Reads DOM: window.scroll) |
          |                                                 | <-----------------------------+
          | <--- 3. Mail back results (deserialize) ------- |
          v                                                 v
[ Process returned value ]                       [ Value exists inside DOM ]
```

1. **You cannot walk inside:** You cannot directly write `const height = window.innerHeight;` in your test script. Node.js does not have a `window` object; trying to reference it throws a crash.
2. **Sending a letter (`page.evaluate`):** You write down instructions inside a function: `() => window.innerHeight`. Playwright serializes this function, sends it across the WebSocket connection to the browser, executes it there, and mails you back the return value.
3. **Passing inventory tools:** If your instruction needs a variable from headquarters (e.g., a specific database threshold), you must pass it explicitly as an argument so Playwright can serialize it. You cannot read variables from the outer Node.js lexical scope.

---

## `page.evaluate` vs. `locator.evaluate`

- **`page.evaluate(fn, arg)`**: Runs the function in the global page scope. Useful for checking general window dimensions, cookies, or calling global functions (e.g., `window.stripeLoaded`).
- **`locator.evaluate(fn, arg)`**: Finds the element first, then passes that element as a native DOM node (e.g., `HTMLElement`) as the first argument to your function. Useful for checking computed styles, dimensions, or firing custom client-side events directly on an element.

---

## Canonical Code Example

This code demonstrates how to retrieve window metrics, access client-side variables, and inspect computed CSS styles directly using evaluation scripts:

```javascript
// @ts-check
const { test, expect } = require("@playwright/test");

test("Inspecting browser-side DOM variables and styles", async ({ page }) => {
  await page.goto("/catalog");

  // 1. page.evaluate: Retrieve general browser window telemetry
  const pageMetrics = await page.evaluate(() => {
    return {
      scrollPosition: window.scrollY,
      pageTitle: document.title,
      devicePixelRatio: window.devicePixelRatio
    };
  });

  console.log(`Scroll position is: ${pageMetrics.scrollPosition}`);

  // 2. Passing arguments: You must pass Node.js variables explicitly
  const targetMultiplier = 2;
  
  // Playwright serializes 'targetMultiplier' and passes it as 'multiplier'
  const computedCalculation = await page.evaluate((multiplier) => {
    // Cannot reference 'targetMultiplier' directly here (ReferenceError!)
    const baseHeight = window.innerHeight;
    return baseHeight * multiplier;
  }, targetMultiplier);

  console.log(`Computed Height: ${computedCalculation}`);

  // 3. locator.evaluate: Inspect detailed element style properties
  const cartIcon = page.locator("#shopping-cart-badge");

  // Playwright locates the element and passes it as the DOM element 'el'
  const cartBadgeColor = await cartIcon.evaluate((el) => {
    // Runs inside the browser DOM context
    return window.getComputedStyle(el).backgroundColor;
  });

  // Check returned CSS value
  expect(cartBadgeColor).toBe("rgb(255, 0, 0)"); // Verify badge is red
});
```

---

## Related
* [[js-dom-tree-and-element-selection]] - Accessing standard DOM structures inside evaluation scripts.
* [[js-json-parse-and-stringify]] - The serialization model underlying WebSocket data transfers between Node.js and Chromium.
* [[js-arrow-functions-and-lexical-this]] - How callback arrows structure scope inside evaluations.
* [[js-json-parse-and-stringify]] - Parameters and return values are serialized as JSON when passed between Node.js and the browser.
* [[js-dom-tree-and-element-selection]] - Evaluates standard document selector scripts directly inside the browser DOM context.
