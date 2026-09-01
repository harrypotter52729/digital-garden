---
id: 202608152109
title: Playwright TypeScript Framework Refactoring and Module Typing
aliases:
  - ts framework refactoring
  - typescript page objects
  - Page and Locator types
  - export class TypeScript
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - tool/typescript
  - lang/ts
  - topic/architecture
date_created: 2026-08-15
mastery_level: 1
---

# Playwright TypeScript Framework Refactoring and Module Typing

---

> **TL;DR:** Refactoring JavaScript Playwright Page Objects to TypeScript requires importing `Page` and `Locator` types from `@playwright/test`, declaring explicit class member types, using `export class ClassName`, and replacing `require()` with ES6 `import` statements.

---

## Why This Exists

In plain JavaScript, Page Object class properties (`this.page`, `this.usernameInput`) are untyped (`any`), meaning IDE autocompletion is unavailable and method parameter typos are caught only at runtime. Refactoring Page Objects to **TypeScript** enforces type safety across DOM element locators (`Locator`) and browser page interfaces (`Page`).

---

## Mental Model

Imagine converting a handwritten blueprint into a CAD engineering schematic.
- **Untyped JavaScript Page Object:** Drawing generic boxes labeled "element" without specifying whether the component is a 12V motor, a copper pipe, or a plastic valve.
- **Typed TypeScript Page Object:** Every component in the CAD schematic explicitly declares its exact specifications (`usernameInput: Locator`, `page: Page`). If you attempt to plug a motor into a water pipe, CAD highlights the error immediately before manufacturing.

---

## How It Works

1. **Import Types and Export Classes (ES6 Module Syntax):**  
   Import `Page` and `Locator` from `@playwright/test` and prefix classes with `export`:
   ```typescript
   import { Page, Locator } from '@playwright/test';

   export class LoginPage {
     // Explicit class property type declarations
     page: Page;
     usernameInput: Locator;
     passwordInput: Locator;
     signInBtn: Locator;

     constructor(page: Page) {
       this.page = page;
       this.usernameInput = page.getByLabel('Username');
       this.passwordInput = page.getByLabel('Password');
       this.signInBtn = page.getByRole('button', { name: 'Sign In' });
     }
   }
   ```
2. **Type Method Parameters:**  
   Enforce type checking on method signatures:
   ```typescript
   async validLogin(userEmail: string, userPass: string): Promise<void> {
     await this.usernameInput.fill(userEmail);
     await this.passwordInput.fill(userPass);
     await this.signInBtn.click();
   }
   ```

---

## Key Characteristics

- **Full IDE Autocompletion:** IntelliSense immediately suggests Playwright `Locator` methods (`.fill()`, `.click()`, `.textContent()`) on class properties.
- **ES6 Module Import/Export Syntax:** Replaces CommonJS `require()` and `module.exports` with clean `import { Class } from './file'` syntax.
- **Compile-Time Safety:** Prevents passing incorrect data types into Page Object action methods.

---

## Common Mistakes

- **Forgetting explicit class property declarations above the constructor:** Omitting `usernameInput: Locator;` declarations above `constructor()`. TypeScript throws `Property 'usernameInput' does not exist on type 'LoginPage'`!
- **Using CommonJS `require()` alongside ES6 `import`:** Mixing `require('./LoginPage')` with TypeScript `import` statements in the same project file. Use ES6 `import` consistently.

---

## Canonical Code Example

```typescript
// Refactored TypeScript Page Object class for dashboard navigation
import { Page, Locator } from '@playwright/test';

export class DashboardPage {
  page: Page;
  productsList: Locator;
  cartBtn: Locator;

  constructor(page: Page) {
    this.page = page;
    this.productsList = page.locator('.card-body');
    this.cartBtn = page.getByRole('button', { name: 'Cart' });
  }

  async searchAndAddProduct(productName: string): Promise<void> {
    const targetCard = this.productsList.filter({ hasText: productName });
    await targetCard.getByRole('button', { name: 'Add to Cart' }).click();
  }

  async navigateToCart(): Promise<void> {
    await this.cartBtn.click();
  }
}
```

---

## Key Takeaways

- Import `Page` and `Locator` from `@playwright/test`.
- Explicitly declare all class properties and their types above `constructor(page: Page)`.
- Use ES6 `export class ClassName` and `import { ClassName } from './path'` statements.

---

## Related

- [[ts-type-annotations-compilation]] — TypeScript type annotations
- [[pw-page-object-model-design-pattern]] — Page Object Model architecture
- [[MOC - Playwright Framework Design]]
