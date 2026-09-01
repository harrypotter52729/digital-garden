---
id: 202608152058
title: Custom Fixture Extensions with test.extend
aliases:
  - custom fixtures
  - test.extend
  - fixture parameterization
  - extending test base
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/architecture
date_created: 2026-08-15
mastery_level: 1
---

# Custom Fixture Extensions with test.extend

---

> **TL;DR:** `test.extend({ customData: ... })` creates customized Playwright `test` objects that automatically inject test data or pre-instantiated Page Objects into test spec signatures alongside built-in fixtures like `page`.

---

## Why This Exists

Importing JSON files and instantiating Page Objects (`const loginPage = new LoginPage(page)`) at the top of every test spec creates repetitive boilerplate code. Playwright allows teams to extend built-in test fixtures using `test.extend()`, binding custom data fixtures or pre-built Page Objects directly into `test()` parameter signatures (e.g., `test('name', async ({ page, customData, loginPage }) => ...)`).

---

## Mental Model

Imagine a rental car service.
- **Standard Built-in Fixture (`{ page }`):** Handing you a standard rental car key. You still have to pack your own GPS, child seat, and ski rack before starting your trip.
- **Custom Extended Fixture (`test.extend()`):** Calling ahead to request a custom car package (`customDataFixture`). When you pick up the car, the GPS is already mounted, the child seat is strapped in, and the ski rack is pre-installed (`async ({ page, loginPage, testData })`). You sit down and drive immediately.

---

## How It Works

1. **Define Base Extension File (`utils/baseTest.js`):**  
   Extend default `test` using `test.extend()` and export the result:
   ```javascript
   const base = require('@playwright/test');

   const customTest = base.test.extend({
     testDataForOrder: {
       userEmail: 'user@test.com',
       productName: 'ZARA COAT 3',
     },
   });

   module.exports = { customTest };
   ```
2. **Import Custom Test in Specs:**  
   Import `customTest` instead of default `@playwright/test`:
   ```javascript
   const { customTest } = require('../utils/baseTest');

   customTest('login using custom fixture', async ({ page, testDataForOrder }) => {
     console.log(testDataForOrder.userEmail); // Injected automatically!
   });
   ```

---

## Key Characteristics

- **Boilerplate Reduction:** Eliminates repetitive `require()` and Page Object instantiation statements across specs.
- **Clean Fixture Signatures:** Access custom fixtures directly in test callback functions (`async ({ page, testData })`).
- **Framework Customization:** Extends Playwright's core runner capabilities natively without third-party plugins.

---

## Common Mistakes

- **Confusing `customTest` with default `test`:** Importing `{ test }` from `@playwright/test` instead of `{ customTest }` from your custom base file. Custom fixtures are only available on the extended `customTest` object!
- **Overusing custom fixtures for multi-data iteration:** Custom fixtures excel at single-dataset injection; use JSON array loops (`for...of`) when running multiple parameterized iterations.

---

## Canonical Code Example

### `utils/baseTest.js`

```javascript
// Extending Playwright test runner with custom fixtures
const base = require('@playwright/test');
const { LoginPage } = require('../pageObjects/LoginPage');

const customTest = base.test.extend({
  // Custom data fixture
  testDataForOrder: {
    userEmail: 'anshika@gmail.com',
    userPassword: 'Iamking@123',
    productName: 'ZARA COAT 3',
  },

  // Custom Page Object fixture (Instantiates LoginPage automatically)
  loginPage: async ({ page }, use) => {
    const loginPage = new LoginPage(page);
    await loginPage.goTo();
    await use(loginPage); // Pass fixture to test
  },
});

module.exports = { customTest };
```

### Spec Usage: `tests/orderWithFixture.spec.js`

```javascript
// Spec consuming custom extended fixtures
const { expect } = require('@playwright/test');
const { customTest } = require('../utils/baseTest');

// Notice: loginPage and testDataForOrder are injected directly into callback signature!
customTest('submit order using custom extended fixtures', async ({ page, loginPage, testDataForOrder }) => {
  // loginPage.goTo() was executed automatically in fixture setup!
  await loginPage.validLogin(testDataForOrder.userEmail, testDataForOrder.userPassword);

  await expect(page.locator('.dashboard-header')).toBeVisible();
});
```

---

## Key Takeaways

- Extend `test` using `base.test.extend({ customFixture: ... })`.
- Custom fixtures inject pre-configured data or Page Objects directly into test callback parameters.
- Reduces spec boilerplate and creates clean test execution signatures.

---

## Related

- [[pw-data-driven-json-parameterization]] — External JSON parameterization
- [[pw-page-object-model-design-pattern]] — Page Object Model architecture
- [[MOC - Playwright Framework Design]]
