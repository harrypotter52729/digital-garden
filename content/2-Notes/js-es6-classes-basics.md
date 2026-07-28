---
id: 202607281950
title: ES6 Class Syntax & Declarations
aliases:
  - ES6 classes
  - JavaScript classes
  - class declaration js
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/objects
  - topic/oop
date_created: {today}
mastery_level: 1
---

# ES6 Class Syntax & Declarations

> **TL;DR:** ES6 `class` syntax is like a modern architectural blueprint stencil laid over JavaScript's legacy prototype factory—it gives developers a clean, familiar syntax for object-oriented design while under the hood it still manufactures prototypes!

---

## Why This Exists
Prior to ES6, object-oriented instantiation required writing constructor functions and manually wiring `Constructor.prototype.method = ...`. This pattern led to boilerplate code, inconsistent inheritance logic, and steep learning curves for developers coming from languages like Java or C++. 

ES6 classes introduce syntactic sugar that encapsulates object construction, prototype methods, getters/setters, static properties, and inheritance into a unified block.

---

## Mental Model
Imagine constructing custom sports cars:
- Pre-ES6: You manually assemble the chassis function (`Car`), then walk over to a separate workbench (`Car.prototype`) and screw on the engine methods one by one.
- ES6 `class`: You draw a single master blueprint containing the ignition key constructor, dashboard getters, and engine methods inside a single tidy schematic block. The assembly factory still creates prototypes, but your blueprint keeps everything organized.

```
       ES6 Class Blueprint                     V8 Engine Prototype Wiring
┌────────────────────────────────┐            ┌───────────────────────────┐
│ class User {                   │            │ User.prototype:           │
│   constructor(name) { ... }    │ ─────────► │   constructor: User       │
│   greet() { ... }              │            │   greet: function()       │
│ }                              │            └───────────────────────────┘
└────────────────────────────────┘
```

---

## How It Works

1. **Syntactic Sugar around Functions:** Under the V8 engine, `class User {}` defines a special function typeof `"function"`.
2. **```[[js-ecmascript-internal-slots|[[Construct]]]]``` Enforcement:** Classes cannot be invoked without `new`. Calling `User()` directly throws a `TypeError: Class constructor User cannot be invoked without 'new'`.
3. **Strict Mode by Default:** The entire body of an ES6 class executes in `"use strict"` mode automatically.
4. **Non-Enumerable Prototype Methods:** Methods declared inside a class body (like `greet()`) are automatically placed on `User.prototype` with `enumerable: false`.

---

## Key Characteristics
- **Not Hoisted:** Unlike function declarations, class declarations exist in a Temporal Dead Zone (TDZ) and cannot be instantiated before their declaration line.
- **Static Members:** The `static` keyword defines utility methods and fields directly on the class constructor itself, not on instance prototypes.
- **Private Fields (`#field`):** Modern ECMAScript supports hard private class fields accessible only inside the class body.

---

## Common Mistakes

### Mistake 1: Invoking Class without `new`
```javascript
class Player {
  constructor(name) { this.name = name; }
}

// CRASH! TypeError: Class constructor Player cannot be invoked without 'new'
const p = Player("Mario");
```

### Mistake 2: Forgetting Class Hoisting Rules
```javascript
// CRASH! ReferenceError: Cannot access 'Vehicle' before initialization
const car = new Vehicle();

class Vehicle {}
```

---

## Canonical Code Example

```javascript
/**
 * Demonstrates modern ES6 Class features: fields, constructor, 
 * prototype methods, getters/setters, static helpers, and private fields.
 */

class BankAccount {
  // Private field (hard privacy encapsulated by JS runtime)
  #balance = 0;

  constructor(owner, initialDeposit) {
    this.owner = owner;
    this.#balance = initialDeposit;
  }

  // Prototype method (non-enumerable)
  deposit(amount) {
    if (amount <= 0) throw new Error("Deposit must be positive.");
    this.#balance += amount;
    return this.#balance;
  }

  // Getter for controlled access
  get balance() {
    return `$${this.#balance.toFixed(2)}`;
  }

  // Static utility method (attached directly to BankAccount constructor)
  static formatCurrency(amount) {
    return new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD' }).format(amount);
  }
}

// Usage Example
const account = new BankAccount("Hemanth", 500);

console.log("Account Owner:", account.owner);       // Expected: "Hemanth"
console.log("Balance Getter:", account.balance);     // Expected: "$500.00"

account.deposit(250);
console.log("Updated Balance:", account.balance);    // Expected: "$750.00"

// Static method call
console.log("Static Format:", BankAccount.formatCurrency(1500)); // Expected: "$1,500.00"

// Encapsulation Verification: Private field is inaccessible outside class
console.log("Private Balance Access:", account.#balance); // SyntaxError / Private field error
```

---

## Key Takeaways
- ES6 `class` is syntactic sugar over JavaScript's existing prototypal inheritance system.
- Class declarations are in the Temporal Dead Zone (TDZ) and require `new` for instantiation.
- Methods defined inside classes are placed on the prototype and are non-enumerable by default.
- Modern classes support static methods, getters/setters, and `#private` hard-encapsulated fields.

---

## Related
- [[js-es6-classes-under-the-hood]] — Deconstructing class compilation into prototype manipulation.
- [[js-class-inheritance-extends-super]] — Subclassing and prototype chaining via `extends`.
- [[js-constructor-functions]] — Legacy pre-ES6 object instantiation pattern.
- [[MOC - JS Objects & Structures]] — Central Objects Map of Content.
