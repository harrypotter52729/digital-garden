---
id: 202606131232
title: Soft Assertions
aliases:
  - playwright-soft-assertions
  - non-blocking-expect
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/assertions
date_created: 2026-06-13
mastery_level: 1
---

# Soft Assertions

> **TL;DR:** Standard assertions (`expect(locator).toBe...`) are **hard assertions**—they immediately crash and end the test upon the first failure. **Soft assertions** (`expect.soft(locator).toBe...`) record the failure but allow the test to continue executing. This lets you gather multiple errors in a single test run rather than fixing them one-by-one.

## The Vehicle Inspection Analogy

Imagine taking your car in for an annual safety inspection:

```
Hard Assertion (Inspector halts inspection immediately):
[ Check 1: Windshield Wipers ] --> Worn out! ❌
[ ACTION ] --> Abort the inspection, tow the car to a garage. 
(You must fix the wipers, pay, and book a new test just to check the brakes).

Soft Assertion (Inspector compiles a report):
[ Check 1: Windshield Wipers ] --> Worn out! (Log error) ❌
[ Check 2: Headlights ]          --> Functional!  ✅
[ Check 3: Brakes ]              --> Squeaking!   (Log error) ❌
[ REPORT ] --> "Inspection completed. Failures: Wipers, Brakes."
(You get a full list of errors to fix in one garage visit).
```

- **Hard Assertions** are critical gatekeepers. If your login test fails to find the dashboard header, there is no point checking the user profile edit box.
- **Soft Assertions** are checklist auditors. If you want to verify that all 10 widgets on a dashboard load their correct text, a failure in widget #2 shouldn't prevent you from seeing if widgets #3 to #10 are also broken.

---

## When to Use Soft Assertions

Use soft assertions when:
- Verifying independent visual cards, text boxes, or navigation links on a page.
- Confirming various properties of a data object.
- Running comprehensive page audit tests.

Do **NOT** use soft assertions when:
- A step is a dependency for the rest of the test. E.g., if you check that `loginSubmit.click()` succeeded, and it fails, doing soft assertions on the user dashboard page will generate cascade failures (e.g., trying to read elements that do not exist), cluttering your report.

---

## Canonical Code Example

This code shows how to verify several visual properties of a product page card without causing the test thread to stop on the first error:

```javascript
// @ts-check
const { test, expect } = require("@playwright/test");

test("Product Detail Page audit using soft assertions", async ({ page }) => {
  await page.goto("/products/item-102");

  // 1. Hard Assertion: Verify the main content wrapper is loaded first.
  // If this fails, the page is empty/broken, so we crash immediately.
  await expect(page.locator(".product-container")).toBeVisible();

  // 2. Soft Assertions: Verify individual card details.
  // If the badge is missing, we still want to confirm the title, price, and button.
  
  // Check A: Product Badge
  await expect.soft(page.locator(".badge-promo")).toHaveText("Sale - 20% Off");

  // Check B: Product Title
  await expect.soft(page.locator("h1.product-title")).toHaveText("Wireless Bluetooth Headphones");

  // Check C: Product Price
  await expect.soft(page.locator(".price-amount")).toHaveText("$89.99");

  // Check D: Add to Cart button is enabled
  await expect.soft(page.getByRole("button", { name: "Add to Cart" })).toBeEnabled();

  // --- Handling Failures ---
  // If any of the soft assertions above failed, Playwright will still execute the 
  // lines below, but it will mark this test as FAILED at the end.
  // The terminal output and HTML report will list every single failed soft expect.
  
  console.log("Completed product card visual audit.");
});
```

---

## Related
* [[pw-web-first-assertions]] - Combining async polling assertions with soft checking execution.
* [[pw-test-runner-and-hooks]] - Understanding how failures inside setup hooks automatically abort test execution before checks begin.
* [[js-try-catch-finally-flow]] - Uses internal try-catch flow to record errors without throwing unhandled exceptions.
