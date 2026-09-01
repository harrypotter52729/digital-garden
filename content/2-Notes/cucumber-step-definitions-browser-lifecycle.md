---
id: 202608152111
title: Cucumber Step Definitions and Browser Lifecycle Setup
aliases:
  - cucumber step definitions
  - Given step binding
  - chromium launch cucumber
  - step parameters regex
tags:
  - type/concept
  - status/processing
  - tool/cucumber
  - tool/playwright
  - topic/execution
date_created: 2026-08-15
mastery_level: 1
---

# Cucumber Step Definitions and Browser Lifecycle Setup

---

> **TL;DR:** Step definition files map plain-English Gherkin lines to Playwright code blocks using `Given('pattern', async function(...) {})`, capturing string arguments in double quotes and managing browser instance launches via `chromium.launch()`.

---

## Why This Exists

Gherkin feature files (`.feature`) contain plain text sentences. To execute those sentences as real automated browser tests, Cucumber requires **Step Definition** files. Step definitions map exact sentence strings (or regular expression patterns) to asynchronous JavaScript functions that invoke Playwright actions.

---

## Mental Model

Imagine a translation headset at an international conference.
- **Gherkin Sentence (`.feature`):** A delegate speaks a sentence in French (`Given I log in as "Admin"`).
- **Step Definition (`steps.js`):** The translator headset matches the exact French sentence pattern, translates it into machine instructions (`await page.fill('#user', username)`), and sends it to the computer.

---

## How It Works

1. **Import Cucumber Keyword Bindings:**  
   Import `Given`, `When`, `Then` from `@cucumber/cucumber`:
   ```javascript
   const { Given, When, Then } = require('@cucumber/cucumber');
   const { chromium } = require('@playwright/test');
   ```
2. **Bind Sentence Patterns & Parameters:**  
   Use string pattern matching or `{string}` parameter placeholders:
   ```javascript
   Given('I log in to e-commerce application with {string} and {string}', async function (username, password) {
     const browser = await chromium.launch({ headless: false });
     const context = await browser.newContext();
     const page = await context.newPage();
     
     await page.goto('https://example.com/login');
     await page.fill('#user', username);
     await page.fill('#pass', password);
   });
   ```
3. **Execution CLI:**  
   Run scenarios via `npx cucumber-js --exit`.

---

## Key Characteristics

- **Parameter Capture `{string}`:** Automatically extracts strings in double quotes from `.feature` lines and passes them as function arguments.
- **Asynchronous Execution:** Callback functions must use `async function()` and `await` Playwright promises.
- **CLI Exit Flag:** Requires `npx cucumber-js --exit` to release terminal processes cleanly after suite completion.

---

## Common Mistakes

- **Using ES6 arrow functions `async () => {}` for step definitions:** Using arrow functions breaks Cucumber's `this` context binding! Always use standard function expressions `async function() {}`.
- **Mismatched step text strings between `.feature` and `.js`:** Having a typo in the step string (e.g., `Given I log in` in `.feature` vs `Given I login` in step `.js`). Cucumber marks the step as `Undefined`!

---

## Canonical Step Definition Code Example (`features/step_definitions/steps.js`)

```javascript
// Cucumber step definition mapping Gherkin steps to Playwright actions
const { Given, When, Then } = require('@cucumber/cucumber');
const { expect, chromium } = require('@playwright/test');

// Note: Using 'async function' to preserve Cucumber 'this' context binding
Given('I log in to e-commerce application with {string} and {string}', { timeout: 10 * 1000 }, async function (username, password) {
  // Explicitly launch browser instance (if not using Hooks or World)
  this.browser = await chromium.launch({ headless: true });
  this.context = await this.browser.newContext();
  this.page = await this.context.newPage();

  await this.page.goto('https://example.com/login');
  await this.page.fill('#username', username);
  await this.page.fill('#password', password);
  await this.page.click('#login-btn');
});

When('I add product {string} to the cart', async function (productName) {
  const card = this.page.locator('.card-body').filter({ hasText: productName });
  await card.getByRole('button', { name: 'Add to Cart' }).click();
});

Then('I verify product is displayed in cart', async function () {
  await expect(this.page.locator('.cart-item')).toBeVisible();
  await this.browser.close();
});
```

---

## Key Takeaways

- Map Gherkin steps to code using `Given('step text {string}', async function(param) {})`.
- Always use `async function()` instead of arrow functions `() => {}` to preserve `this` context.
- Capture dynamic string parameters using `{string}` placeholders.

---

## Related

- [[cucumber-bdd-gherkin-feature-architecture]] — Gherkin feature architecture
- [[cucumber-world-context-lifecycle-hooks]] — Cucumber World context and hooks
- [[MOC - Playwright Framework Design]]
