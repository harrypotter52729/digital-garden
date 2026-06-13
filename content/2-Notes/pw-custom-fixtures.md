---
id: 202606131236
title: Custom Fixtures
aliases:
  - playwright-fixtures
  - custom-fixtures
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/framework-design
date_created: 2026-06-13
mastery_level: 1
---

# Custom Fixtures

> **TL;DR:** Playwright **Fixtures** provide an isolated, reusable testing environment setup. By using `test.extend()`, you can declare custom fixtures that bundle your page object configurations or data initializations, making them available as parameters directly in your tests. This eliminates the boilerplates of importing, instantiating, and cleaning up classes inside individual test blocks.

## The Workstation Prep Analogy

Imagine a chef preparing a specialized dish in a professional kitchen:

```
Using traditional hooks (beforeEach/afterEach):
[ Chef enters ] -> Must search for knives, wash pans, unpack spices (manual setup).
[ Cooking ]     -> Executes the recipe.
[ Cleanup ]     -> Must wash all pans and return knives to racks (manual teardown).

Using Custom Fixtures:
[ Prep Assistant ] -> Arranges the knives, washes the pans, portion-sizes the spices.
[ Chef enters ]    -> Finds a fully-stocked workstation. Focuses 100% on cooking.
[ Chef leaves ]    -> Prep Assistant returns and cleans everything up automatically.
```

- **The Chef:** Your test block (`test("Should checkout item", async ({ checkoutPage }) => { ... })`).
- **The Prep Assistant:** A **Custom Fixture** that initializes `checkoutPage` before the test, serves it to the test parameters, and tears it down after execution.

---

## Why Fixtures are Superior to Hooks

1. **Lazy Loading:** Playwright only runs a fixture if a test explicitly asks for it in its arguments. Hooks, by comparison, run for *every* test in the file, wasting compute cycles.
2. **Encapsulated Setup/Teardown:** A fixture contains both its setup and teardown logic in a single function using a `yield` statement. Setup happens *before* `use()`, and teardown runs *after* the test completes.
3. **Composable Dependency Trees:** Fixtures can depend on other fixtures. For example, a `loggedInPage` fixture can depend on the native `page` fixture, navigate, log in, and then pass the session back.

---

## Canonical Code Example

This code extends Playwright's test runner to create two custom fixtures: a basic `loginPage` instance and a pre-authenticated `adminDashboardPage` context.

### Extending the Test Runner (`fixtures/my-test-fixtures.js`)

```javascript
// @ts-check
const base = require("@playwright/test");
const { LoginPage } = require("../pages/LoginPage");
const { AdminDashboardPage } = require("../pages/AdminDashboardPage");

// Extend the base test to register custom fixtures
const test = base.test.extend({
  // Fixture 1: Automatically instantiates LoginPage
  loginPage: async ({ page }, use) => {
    const loginPage = new LoginPage(page);
    await loginPage.navigate();
    
    // Pass the initialized page object to the test
    await use(loginPage);
    
    // Optional: Teardown logic runs here after the test finishes
    console.log("Teardown loginPage fixture...");
  },

  // Fixture 2: Sets up a pre-logged in Admin state
  adminDashboardPage: async ({ page }, use) => {
    const loginPage = new LoginPage(page);
    const adminDashboard = new AdminDashboardPage(page);
    
    // Execute login sequence
    await loginPage.navigate();
    await loginPage.login("admin_user", "AdminSecretPass!");
    
    // Serve the dashboard object to the test
    await use(adminDashboard);
  }
});

// Export custom test and expect definitions
module.exports = { test, expect: base.expect };
```

### The Clean Test File (`tests/dashboard.spec.js`)

```javascript
// @ts-check
// Import the custom 'test' runner, NOT the default one from @playwright/test
const { test, expect } = require("../fixtures/my-test-fixtures");

test("Standard user login page validates forms", async ({ loginPage }) => {
  // loginPage is already instantiated and navigated to /login!
  await loginPage.login("user", "incorrect");
  await expect(loginPage.errorMessage).toBeVisible();
});

test("Admin can review logs on dashboard", async ({ adminDashboardPage }) => {
  // adminDashboardPage is already logged in as admin!
  await adminDashboardPage.navigateToLogs();
  await expect(adminDashboardPage.logTable).toContainText("System restarted");
});
```

---

## Related
* [[pw-page-object-model-pattern]] - Creating Page Object Model files that are wrapped inside custom fixtures.
* [[pw-test-runner-and-hooks]] - How Playwright's runner controls hooks versus utilizing lazy fixtures.
* [[js-higher-order-functions-and-callbacks]] - Injects pre-configured page objects using higher-order callback wrappers.
