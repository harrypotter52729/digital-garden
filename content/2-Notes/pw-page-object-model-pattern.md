---
id: 202606131235
title: Page Object Model Pattern
aliases:
  - page-object-model
  - pom-pattern
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/framework-design
date_created: 2026-06-13
mastery_level: 1
---

# Page Object Model Pattern

> **TL;DR:** The **Page Object Model (POM)** is a design pattern that encapsulates the structure and behavior of a web page into a reusable JavaScript Class. By separating page selectors and action scripts from the actual test assertions, you avoid code duplication and simplify test maintenance when application markups change.

## The Office Directory Analogy

Imagine your company has a set of written onboarding procedures for employees:

```
Without a Central Directory:
Instructions for 20 employees say: "Walk down the hall and make copies using the machine in Room 102."
*What happens if the copier moves to Room 304?* 
You must locate and rewrite all 20 instruction documents manually!

With a Central Directory (POM):
The directory says: "The copier is in Room 102."
Instructions say: "Consult the directory to find the copier, then make copies."
*What happens if the copier moves to Room 304?*
You edit ONE line in the central directory. Every employee's instructions remain valid.
```

In automated UI testing:
- **The Central Directory:** The POM Class containing locators (e.g., `this.copierRoomInput`).
- **The Employee Instructions:** Your Test files (`dashboard.spec.js`) containing clean steps.

---

## Benefits of Page Objects

1. **Dry Code (Don't Repeat Yourself):** Instead of rewriting `page.locator('#email-input')` in ten different test files, you define it once inside your class.
2. **Encapsulation:** Tests only concern themselves with *what* behavior they are validating, while POM classes handle *how* to interact with the DOM elements to produce that behavior.
3. **Resilience:** If developers change class names or DOM structures, you only update the locators in your class file. Your test scripts remain untouched.

---

## Canonical Code Example

This setup shows a clean Page Object Class and a test that imports and executes actions through it:

### The Page Object Class (`pages/LoginPage.js`)

```javascript
// @ts-check
const { expect } = require("@playwright/test");

class LoginPage {
  /**
   * @param {import('@playwright/test').Page} page
   */
  constructor(page) {
    this.page = page;
    
    // 1. Centralize selectors as properties using built-in locators
    this.usernameInput = page.getByLabel("Username");
    this.passwordInput = page.getByPlaceholder("Enter password");
    this.submitBtn = page.getByRole("button", { name: "Sign In" });
    this.errorMessage = page.locator(".error-banner");
  }

  // 2. Encapsulate page navigation
  async navigate() {
    await this.page.goto("/login");
  }

  // 3. Encapsulate common user flows (actions)
  async login(username, password) {
    await this.usernameInput.fill(username);
    await this.passwordInput.fill(password);
    await this.submitBtn.click();
  }
}

module.exports = { LoginPage };
```

### The Test File (`tests/login.spec.js`)

```javascript
// @ts-check
const { test, expect } = require("@playwright/test");
const { LoginPage } = require("../pages/LoginPage");

test("Should show error on invalid credentials", async ({ page }) => {
  // Instantiate the page object, passing the isolated browser page context
  const loginPage = new LoginPage(page);

  // Execute actions defined inside the POM
  await loginPage.navigate();
  await loginPage.login("wrong_user", "BadPassword123");

  // Keep test assertions in the test file itself (assertion separation)
  await expect(loginPage.errorMessage).toHaveText("Invalid username or password.");
});
```

---

## Related
* [[pw-built-in-locators-vs-css-xpath]] - Locating elements securely inside POM classes.
* [[pw-custom-fixtures]] - Standardizing the instantiation of POM classes to make tests even cleaner.
* [[js-es6-classes-under-the-hood]] - POM structures encapsulate page locators and actions within standard ES6 classes.
