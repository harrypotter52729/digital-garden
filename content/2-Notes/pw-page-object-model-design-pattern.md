---
id: 202608152056
title: Page Object Model (POM) Design Pattern and Encapsulation Architecture
aliases:
  - POM design pattern
  - Page Object Model
  - page object architecture
  - constructor(page)
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/architecture
date_created: 2026-08-15
mastery_level: 1
---

# Page Object Model (POM) Design Pattern and Encapsulation Architecture

---

> **TL;DR:** The Page Object Model (POM) encapsulates web page locators and action methods inside ES6 JavaScript classes—separating UI selectors from test specs so UI changes require updating code in only one central class file.

---

## Why This Exists

Writing element locators (`#user-email`, `#btn-submit`) directly inside test spec files creates massive code duplication. If a web developer renames a button ID, engineers must search and update 50 different spec files. The **Page Object Model (POM)** pattern encapsulates locators and user actions into dedicated page classes (e.g., `LoginPage.js`), providing clean, reusable methods (`loginPage.validLogin(user, pass)`) for test specs.

---

## Mental Model

Imagine a company director interacting with different office departments.
- **Un-encapsulated Test Spec:** The director personally walks down to the basement, operates the printing press, stamps envelopes, and carries mail to the post office. If the post office moves, the director's daily routine breaks.
- **Page Object Model (`LoginPage`):** The director calls the Mailroom Manager (`loginPage.sendMail(content)`). The Mailroom Manager handles printing, stamping, and post office delivery internally. If the post office address changes, only the Mailroom Manager updates their routine—the director's request stays identical.

---

## Architecture Flow

```text
┌──────────────────────────────────────────┐
│             Test Spec File               │
│   (e.g., tests/loginVerification.spec)   │
└──────────────────────────────────────────┘
                     │
                     │ Calls Encapsulated Methods
                     ▼
┌──────────────────────────────────────────┐
│          Page Object Class               │
│        (e.g., pageObjects/LoginPage.js)   │
├──────────────────────────────────────────┤
│ - constructor(page)                      │
│ - locators: username, password, submit   │
│ - methods: goTo(), validLogin()          │
└──────────────────────────────────────────┘
                     │
                     │ Interacts With
                     ▼
┌──────────────────────────────────────────┐
│             Web Application UI           │
└──────────────────────────────────────────┘
```

---

## How It Works

1. **Class Definition & Constructor Injection:**  
   Define an ES6 class and receive Playwright's `page` fixture in the constructor, assigning it to `this.page`:
   ```javascript
   class LoginPage {
     constructor(page) {
       this.page = page;
       this.usernameInput = page.getByLabel('Username');
       this.passwordInput = page.getByLabel('Password');
       this.signInBtn = page.getByRole('button', { name: 'Sign In' });
     }
   }
   ```
2. **Encapsulated Action Methods:**  
   Define high-level asynchronous methods:
   ```javascript
   async goTo() {
     await this.page.goto('https://example.com/login');
   }

   async validLogin(username, password) {
     await this.usernameInput.fill(username);
     await this.passwordInput.fill(password);
     await this.signInBtn.click();
   }
   ```
3. **Module Export & Spec Usage:**  
   Export using `module.exports = { LoginPage };`. Instantiate and call in spec files:
   ```javascript
   const loginPage = new LoginPage(page);
   await loginPage.goTo();
   await loginPage.validLogin('user', 'pass');
   ```

---

## Key Characteristics

- **High Maintainability:** UI selector updates require modifying only a single Page Object class.
- **Readable Test Specs:** Specs read like plain-English business actions (`await loginPage.validLogin(user, pass)`).
- **Decoupled Architecture:** Separates test data, assertions, and UI locators cleanly.

---

## Common Mistakes

- **Placing assertions inside Page Object classes:** Putting `expect()` statements inside POM action methods. Keep assertions in test spec files; POM classes should handle UI interaction and return data/locators.
- **Forgetting `this.` when referencing class properties:** Writing `usernameInput.fill()` inside class methods instead of `this.usernameInput.fill()`.

---

## Canonical Code Example

### `pageObjects/LoginPage.js`

```javascript
// Encapsulating login page locators and action methods in a Page Object class
class LoginPage {
  constructor(page) {
    this.page = page;
    this.usernameInput = page.getByLabel('Username');
    this.passwordInput = page.getByLabel('Password');
    this.signInBtn = page.getByRole('button', { name: 'Sign In' });
  }

  async goTo() {
    await this.page.goto('https://example.com/login');
  }

  async validLogin(username, password) {
    await this.usernameInput.fill(username);
    await this.passwordInput.fill(password);
    await this.signInBtn.click();
  }
}

module.exports = { LoginPage };
```

### Spec Usage: `tests/login.spec.js`

```javascript
// Clean test spec utilizing Page Object Model abstractions
const { test, expect } = require('@playwright/test');
const { LoginPage } = require('../pageObjects/LoginPage');

test('user login flow using Page Object Model', async ({ page }) => {
  const loginPage = new LoginPage(page);

  // 1. Navigate to login page
  await loginPage.goTo();

  // 2. Perform valid login action
  await loginPage.validLogin('admin_user', 'SecretPassword123');

  // 3. Perform assertion in spec file
  await expect(page.locator('.dashboard-header')).toBeVisible();
});
```

---

## Key Takeaways

- Encapsulate page locators in constructor properties (`this.usernameInput = page.locator(...)`).
- Encapsulate UI action flows in asynchronous class methods (`async validLogin()`).
- Keep `expect()` assertions inside test spec files, leaving POM classes focused on UI mechanics.

---

## Related

- [[pw-refactoring-accessibility-locators]] — Accessible locator strategy
- [[pw-api-utils-class-abstraction]] — API utility class abstraction
- [[MOC - Playwright Framework Design]]
