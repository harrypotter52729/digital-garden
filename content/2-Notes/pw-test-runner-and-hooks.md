---
id: 202606131224
title: Test Runner & Hooks
aliases:
  - playwright-hooks
  - test-execution-lifecycle
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/architecture
date_created: 2026-06-13
mastery_level: 1
---

# Test Runner & Hooks

> **TL;DR:** Playwright's test runner controls the lifecycle of your tests using **Hooks** (`beforeAll`, `beforeEach`, `afterEach`, `afterAll`). Hooks manage the setup and teardown tasks needed to prepare clean, consistent environments before tests run and clean up after they finish.

## The Stage Play Analogy

Think of executing a test suite like staging a **theater play** with multiple scenes:

```
[ beforeAll ]  --> Build the stage set (Only once at the start)
    |
    +--> [ beforeEach ]  --> Clear props, put actor in starting spot (Before Scene 1)
    |         |
    |    { Test / Scene 1 }
    |         |
    +--> [ afterEach ]   --> Sweep stage, remove scene-specific clutter (After Scene 1)
    |
    +--> [ beforeEach ]  --> Reset props, put actor in starting spot (Before Scene 2)
    |         |
    |    { Test / Scene 2 }
    |         |
    +--> [ afterEach ]   --> Sweep stage, remove scene-specific clutter (After Scene 2)
    |
[ afterAll ]   --> Tear down the entire set and vacate theater (Only once at the end)
```

- **`beforeAll` (Building the Set):** Runs once *before all tests* in a file. Use this for heavy, expensive tasks that you only want to do once, like seeding a database or generating a global auth token.
- **`beforeEach` (Resetting the Props):** Runs *before every single test*. Use this to navigate to a starting URL, log in, or set up page-specific data.
- **`afterEach` (Cleaning the Clutter):** Runs *after every single test*. Use this to log out, reset cookies, or clean up test data.
- **`afterAll` (Vacating the Theater):** Runs once *after all tests* in a file have completed. Use this to close database connections or save final test execution metrics.

---

## Order of Execution

When you nest hooks inside `test.describe` blocks, they follow a predictable tree-like ordering (outside-in for setups, inside-out for teardowns):

1. **Outer** `beforeAll`
2. **Inner** `beforeAll`
3. **Outer** `beforeEach`
4. **Inner** `beforeEach`
5. **`test` Body**
6. **Inner** `afterEach`
7. **Outer** `afterEach`
8. **Inner** `afterAll`
9. **Outer** `afterAll`

---

## Best Practice: Avoid Global State Leakage

A common anti-pattern in test automation is declaring a global variable (e.g., `let page;`) at the top of a file, setting it in `beforeAll`, and sharing it across multiple tests. 

Since Playwright runs tests in **parallel**, sharing a single mutable global variable leads to **test collision** (Test A is trying to log in while Test B is clicking a checkout button on the same page, causing both to fail).

### The Correct Way
Always use the built-in `{ page }` fixture in hooks and tests. Playwright automatically handles isolation so that each test runs in its own independent browser context.

---

## Canonical Code Example

Here is a fully commented, production-grade test file demonstrating hooks, nested suites, and proper page context usage:

```javascript
// @ts-check
const { test, expect } = require("@playwright/test");

// Declaring global configs (immutable constants are safe; mutable variables are not)
const TEST_USER = "sdet_test_user";

test.describe("User Profile Suite", () => {
  
  // 1. beforeAll: Sets up global requirements once for this file
  test.beforeAll(async () => {
    console.log(">> SYSTEM LOG: Connecting to the test database...");
    // (e.g., seeding users or resetting test environment state)
  });

  // 2. beforeEach: Prepares a fresh state for each test run
  // Playwright passes the isolated 'page' fixture directly into the hook
  test.beforeEach(async ({ page }) => {
    console.log(">> SYSTEM LOG: Navigating to landing page & logging in user...");
    await page.goto("/login");
    await page.fill("#username", TEST_USER);
    await page.fill("#password", "Password123!");
    await page.click("#login-submit");
  });

  // 3. test: A single independent check
  test("User should see dashboard greeting", async ({ page }) => {
    const greeting = page.locator("#welcome-message");
    await expect(greeting).toHaveText(`Welcome back, ${TEST_USER}!`);
  });

  // 4. nested describe: Inherits outer hooks and adds its own specific hooks
  test.describe("Settings Management", () => {
    
    // Runs only before tests inside this nested block
    test.beforeEach(async ({ page }) => {
      console.log(">> SYSTEM LOG: Navigating specifically to user settings tab...");
      await page.click("#nav-settings");
    });

    test("User can toggle dark mode", async ({ page }) => {
      const darkModeToggle = page.locator("#dark-mode-switch");
      await darkModeToggle.click();
      await expect(darkModeToggle).toBeChecked();
    });
  });

  // 5. afterEach: Cleanup logic run after every test
  test.afterEach(async ({ page }) => {
    console.log(">> SYSTEM LOG: Logging out user to clean context session...");
    await page.click("#logout-btn");
  });

  // 6. afterAll: Global teardown once the whole suite finishes
  test.afterAll(async () => {
    console.log(">> SYSTEM LOG: Disconnecting from the test database.");
  });
});
```

---

## Related
* [[pw-browser-context-and-page-model]] - Explains how the `{ page }` fixture provides clean execution environments for hooks.
* [[pw-playwright-config-file]] - Global timeout configurations controlling hook execution limits.
* [[js-async-await-syntax]] - How async/await works when coordinating sequential hook setups.
* [[js-async-await-syntax]] - Execution lifecycle hooks require async/await to coordinate setup and cleanup order.
