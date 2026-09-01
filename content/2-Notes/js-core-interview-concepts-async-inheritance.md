---
id: 202608152120
title: Core JavaScript Interview Concepts: Asynchronous Execution, Inheritance, and Equality
aliases:
  - js interview concepts
  - callbacks vs promises
  - super and this keywords
  - equality comparison == vs ===
  - null vs undefined
tags:
  - type/concept
  - status/processing
  - tool/node
  - lang/js
  - topic/interview
date_created: 2026-08-15
mastery_level: 1
---

# Core JavaScript Interview Concepts: Asynchronous Execution, Inheritance, and Equality

---

> **TL;DR:** Understanding core JavaScript interview topics—`var` vs `let` vs `const`, Callbacks vs Promises, ES6 class inheritance with `super()` and `this`, strict equality (`===`), and `null` vs `undefined`—is essential for technical interviews and writing clean Playwright automation code.

---

## Why This Exists

Technical test automation interviews frequently evaluate fundamental JavaScript mechanics before testing framework knowledge. Concepts like asynchronous event loops, Promise chains, prototypal inheritance via `super()`, array transformations (`map`, `filter`, `reduce`), and JavaScript type coercion subtleties (`==` vs `===`) form the foundation of senior automation engineering.

---

## Technical Interview Concepts Matrix

### 1. Variable Scope: `var` vs `let` vs `const`
- **`var`:** Function-scoped, hoisted to top of scope with `undefined`, allows re-declaration.
- **`let`:** Block-scoped (`{}`), hoisted in Temporal Dead Zone (TDZ), re-assignable but *cannot* be re-declared in same scope.
- **`const`:** Block-scoped (`{}`), cannot be re-assigned or re-declared (though object properties *can* be mutated).

### 2. Equality Comparison: Abstract (`==`) vs Strict (`===`)
- **Abstract Equality (`==`):** Performs implicit type coercion before comparing values (`5 == "5"` evaluates to `true`).
- **Strict Equality (`===`):** Compares both value AND data type without coercion (`5 === "5"` evaluates to `false`).

### 3. Presence & Identity: `null` vs `undefined`
- **`undefined`:** Variable has been declared but has not been assigned a value (or function returns nothing by default). `typeof undefined === "undefined"`.
- **`null`:** Intentional assignment representing the explicit absence of any object value. `typeof null === "object"` (historical JS quirk).

### 4. Callbacks vs Promises vs `async/await`
- **Callback:** A function passed as an argument to another function to execute after an async task finishes. Nested callbacks lead to "Callback Hell".
- **Promise:** An object representing eventual completion/failure of an async operation (`Pending`, `Fulfilled`, `Rejected`).
- **`async/await`:** ES8 syntactic sugar built on top of Promises, making async code read like synchronous sequential code.

---

## Canonical ES6 Class Inheritance Example

```javascript
// Demonstrating class inheritance, constructor call via super(), and methods
class BasePage {
  constructor(pageName) {
    this.pageName = pageName;
  }

  logNavigation() {
    console.log(`Navigated to page: ${this.pageName}`);
  }
}

class LoginPage extends BasePage {
  constructor(pageName, url) {
    // Call parent class constructor using super()
    super(pageName);
    this.url = url; // Assign child property using 'this'
  }

  displayPageDetails() {
    // Invoke parent class method
    super.logNavigation();
    console.log(`URL: ${this.url}`);
  }
}

const loginPage = new LoginPage('Customer Login', 'https://example.com/login');
loginPage.displayPageDetails();
// Output:
// Navigated to page: Customer Login
// URL: https://example.com/login
```

---

## Array Transformations (`map`, `filter`, `reduce`)

```javascript
// Data processing pipeline using map, filter, and reduce
const products = [
  { name: 'ZARA COAT', price: 299, inStock: true },
  { name: 'ADIDAS SHOES', price: 350, inStock: false },
  { name: 'IPHONE 13', price: 999, inStock: true },
];

// 1. Filter: Keep only items in stock
const availableProducts = products.filter((item) => item.inStock);

// 2. Map: Extract prices of available items
const prices = availableProducts.map((item) => item.price);

// 3. Reduce: Calculate total price of available items
const totalCost = prices.reduce((acc, curr) => acc + curr, 0);

console.log(`Total In-Stock Inventory Value: $${totalCost}`); // Output: $1298
```

---

## Key Takeaways

- Use `===` for strict value and type equality checks.
- Use `super()` inside child class constructors to invoke parent constructors before referencing `this`.
- `undefined` means unassigned variable; `null` represents an explicit intentional empty assignment.
- Transform array data using chainable `filter()`, `map()`, and `reduce()` methods.

---

## Related

- [[js-objects-vs-primitives]] — JS data types and primitives
- [[js-constructor-functions]] — Constructor functions and prototypes
- [[MOC - JS Variables & Execution]]
