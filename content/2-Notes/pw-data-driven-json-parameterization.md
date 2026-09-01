---
id: 202608152057
title: Data-Driven Testing with External JSON and Parameterized Arrays
aliases:
  - data driven testing
  - json parameterization
  - test data array iteration
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/execution
date_created: 2026-08-15
mastery_level: 1
---

# Data-Driven Testing with External JSON and Parameterized Arrays

---

> **TL;DR:** Importing an external JSON array (`data.json`) and wrapping `test()` calls inside a `for...of` loop executes the exact same test spec multiple times against different datasets with dynamic parameterized test titles.

---

## Why This Exists

Hardcoding test data (usernames, passwords, target product names) directly inside test specs makes suites rigid and difficult to update. If product managers request testing 5 different product checkout scenarios, duplicating the entire test spec 5 times creates severe maintenance burden. Playwright enables **Data-Driven Testing** by driving test execution directly from external JSON arrays.

---

## Mental Model

Imagine a cookie cutter on a factory conveyor belt.
- **Hardcoded Approach:** Buying 5 separate identical metal cutters to stamp out 5 cookies one by one.
- **Data-Driven Parameterized Approach:** Mounting a single master cookie cutter (`test()`) on a motorized arm over a moving conveyor belt (`for (const data of dataset)`). As each unique dough batch (`data` object) rolls under the cutter, the master arm stamps out a cookie tailored to that specific batch.

---

## How It Works

1. **Construct External JSON Data Array (`utils/data.json`):**
   ```json
   [
     { "userEmail": "user1@test.com", "productName": "ZARA COAT 3" },
     { "userEmail": "user2@test.com", "productName": "ADIDAS ORIGINAL" }
   ]
   ```
2. **Convert JSON to JavaScript Objects:**  
   Import and parse JSON data in the test spec:
   ```javascript
   const testDataset = JSON.parse(JSON.stringify(require('../utils/data.json')));
   ```
3. **Iterate & Parameterize Test Titles:** Wrap `test()` inside a `for...of` loop, interpolating title strings (`${data.productName}`):
   ```javascript
   for (const data of testDataset) {
     test(`checkout flow for product ${data.productName}`, async ({ page }) => {
       // Test steps using data.userEmail and data.productName...
     });
   }
   ```

---

## Key Characteristics

- **Dynamic Test Titles:** Uses template literals (`test('checkout for ${data.name}')`) to generate distinct pass/fail rows in HTML reports.
- **Decoupled Test Data:** Non-technical business users can update `data.json` without touching spec files.
- **Scalable Dataset Execution:** Adding 10 new test data rows in JSON automatically executes 10 new test runs.

---

## Common Mistakes

- **Static Non-Unique Test Titles inside Loops:** Writing `test('checkout test', ...)` inside a `for` loop without template literal parameterization (`${data.id}`). Playwright throws an error if multiple tests share identical title strings.
- **Modifying JSON objects in-place during tests:** Mutating `data` object properties inside test runs, affecting subsequent test iterations.

---

## Canonical Code Example

```javascript
// Parameterized Data-Driven testing using external JSON data array
const { test, expect } = require('@playwright/test');
const { LoginPage } = require('../pageObjects/LoginPage');

// Import and parse external JSON dataset array
const dataset = JSON.parse(JSON.stringify(require('../utils/placeOrderData.json')));

for (const data of dataset) {
  // DYNAMIC TITLE: Interpolate product name to create unique test names
  test(`e2e checkout flow for product "${data.productName}"`, async ({ page }) => {
    const loginPage = new LoginPage(page);

    await loginPage.goTo();
    await loginPage.validLogin(data.userEmail, data.userPassword);

    // Select parameterized target product
    const productCard = page.locator('.card-body').filter({ hasText: data.productName });
    await productCard.getByRole('button', { name: 'Add to Cart' }).click();

    // Verify product added
    await expect(page.getByRole('link', { name: 'Cart' })).toContainText('1');
  });
}
```

---

## Key Takeaways

- Parse external JSON arrays into JavaScript objects using `JSON.parse(JSON.stringify(require('file.json')))`.
- Wrap `test()` calls in `for...of` loops to execute specs against multiple datasets.
- Interpolate parameter variables inside test titles (`test('flow for ${data.item}')`) to avoid duplicate name collisions.

---

## Related

- [[pw-custom-fixture-extensions]] — Passing test data via custom fixtures
- [[pw-page-object-model-design-pattern]] — Page Object Model architecture
- [[MOC - Playwright Framework Design]]
