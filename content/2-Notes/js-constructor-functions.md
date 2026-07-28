---
id: 202607280809
title: Constructor Functions and the new Keyword
aliases:
  - constructor functions
  - new keyword
  - constructor pattern
  - ``[[js-ecmascript-internal-slots|[[Construct]]]]``
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/object-creation
  - topic/oop
date_created: 2026-07-28
mastery_level: 1
---

# Constructor Functions and the `new` Keyword

> **TL;DR:** A constructor function is a factory mold for casting objects, and the `new` keyword is the automated hydraulic press that feeds a blank memory chunk into the mold, binds `this` to it, executes the custom design, and hands you the completed object back automatically!

---

## Why This Exists
Before ES6 `class` syntax was introduced, JavaScript relied entirely on regular functions combined with the `new` operator to implement Object-Oriented Programming (OOP) and instance instantiation. 

Creating multiple similar objects via object literals `{}` duplicates property declarations and methods across memory. Constructor functions combined with prototype attachment allow developers to instantiate structured object instances that share a single set of methods in memory while maintaining isolated instance state.

---

## Mental Model
Imagine a cookie cutter.
- Constructor Function: The blueprints and shape specs for making a cookie.
- `new` Keyword: The baker's assistant who automatically:
  1. Grabs a fresh slab of dough (creates a fresh empty object `{}`).
  2. Stamps the baker's mark on the bottom (links `[[js-prototype-chain-mechanics|Prototype]]` to `Constructor.prototype`).
  3. Hands the dough to the master baker (`this`) to add custom toppings (assigns instance properties).
  4. Delivers the baked cookie to the customer (returns the created object automatically).

```
new User("Luffy", 26)
        │
        ├─► 1. Create Empty Object {}
        ├─► 2. Link [[js-prototype-chain-mechanics|Prototype]] ──► User.prototype
        ├─► 3. Bind 'this' to {} & Execute User("Luffy", 26)
        └─► 4. Return Object Reference Automatically
```

---

## How It Works

When a function is invoked with the `new` operator (e.g., `new User("Alice")`), the engine executes the ECMAScript internal ```[[js-ecmascript-internal-slots|[[Construct]]]]``` algorithm step-by-step:

1. **Allocation:** Creates a new, blank plain JavaScript object: `const instance = {}`.
2. **Prototype Binding:** Sets `instance.[[js-prototype-chain-mechanics|Prototype]]` to `ConstructorFunction.prototype`.
3. **Execution & `this` Binding:** Invokes `ConstructorFunction` with `this` bound to the newly created `instance`, passing all supplied arguments.
4. **Return Resolution:**
   - If the constructor function explicitly returns a **non-primitive object**, that returned object overrides the created `instance`.
   - If the constructor returns a primitive or nothing (`undefined`), the engine automatically returns the newly created `instance`.

---

## Key Characteristics
- **Naming Convention:** Constructor functions are named with `PascalCase` by community convention (e.g., `User`, `Car`).
- **Automatic `this` Return:** Unless explicitly returning a custom object, `new` automatically returns `this`.
- **Shared Prototype Methods:** Methods attached to `Constructor.prototype` are shared by all instances, preventing memory duplication.

---

## Common Mistakes

### Mistake 1: Invoking a constructor function without the `new` keyword
Calling `User("Alice")` without `new` executes it as a regular function invocation!

```javascript
function User(name) {
  this.name = name;
}

// WRONG: Forgot 'new'!
const user = User("Alice"); 

console.log(user); // Output: undefined!
console.log(window.name); // Output: "Alice" (Polluted global scope in non-strict mode!)
```

### Mistake 2: Defining methods directly inside the constructor body
Placing methods inside `this.method = function() {}` recreates a brand new function object in heap memory for EVERY instance!

```javascript
function User(name) {
  this.name = name;
  // BAD: Creates 1,000 separate function instances in memory for 1,000 users!
  this.greet = function() { console.log(this.name); };
}

// GOOD: Attach to prototype (Single function in memory shared by all instances):
User.prototype.greet = function() { console.log(this.name); };
```

---

## Best Practices
- **Always capitalize constructor functions** (`PascalCase`).
- **Use `new.target`** inside constructor functions to defend against missing `new` keywords.
- **Attach instance methods to `Constructor.prototype`**, reserving the constructor body strictly for instance property assignments.

---

## Comparison Table

| Attribute / Step | Regular Function Call `User()` | Constructor Invocation `new User()` |
| :--- | :--- | :--- |
| **`this` Binding** | `undefined` (strict mode) / `window` | Freshly allocated `{}` object |
| **`[[js-prototype-chain-mechanics|Prototype]]` Setup**| Standard Function prototype lookup | Instance points to `User.prototype` |
| **Return Value** | Explicit `return` value (or `undefined`)| Automatic `this` instance return |
| **`new.target` Value** | `undefined` | Points to `User` function reference |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates Constructor Function mechanics, defensive new.target guards,
 * and memory-efficient prototype method attachment.
 */

// 1. Constructor Function Definition with Defensive Guard
function Product(id, name, price) {
  // Guard against missing 'new' keyword using new.target
  if (!new.target) {
    return new Product(id, name, price);
  }

  // Instance Property Initialization
  this.id = id;
  this.name = name;
  this.price = price;
}

// 2. Prototype Method Attachment (Shared across memory)
Product.prototype.getFormattedPrice = function() {
  return `$${this.price.toFixed(2)}`;
};

Product.prototype.applyDiscount = function(percent) {
  this.price -= this.price * (percent / 100);
  return this.price;
};

// 3. Instantiation via 'new'
const laptop = new Product(101, "Laptop", 1200);
const mouse = Product(102, "Mouse", 25); // Safely auto-constructs thanks to new.target!

// 4. Verification
console.log("Laptop Price:", laptop.getFormattedPrice()); // Expected: "$1200.00"
console.log("Mouse Price:", mouse.getFormattedPrice());   // Expected: "$25.00"

// 5. Verifying Prototype Sharing (Single Function Reference in Memory)
console.log("Shared Method Reference:", laptop.getFormattedPrice === mouse.getFormattedPrice); 
// Expected Output: true (Shared memory reference on Product.prototype!)

console.log("Instance prototype link:", Object.getPrototypeOf(laptop) === Product.prototype); 
// Expected Output: true
```

---

## Key Takeaways
- Invoking a function with `new` performs 4 automated steps: allocates `{}` object, links `[[js-prototype-chain-mechanics|Prototype]]` to `Constructor.prototype`, executes constructor binding `this`, and returns the instance.
- Methods should be declared on `Constructor.prototype` to save memory across multiple instances.
- Forgetting `new` on a constructor function causes `this` to point to global scope (or `undefined` in strict mode).
- Use `new.target` inside constructor logic to safely detect and self-correct invocations missing `new`.

---

## Related
- [[js-this-constructor-invocation]] — Explains how execution context `this` behaves under `new`.
- [[js-constructor-prototype-vs-instance-prototype]] — Distinguishing `.prototype` vs `[[js-prototype-chain-mechanics|Prototype]]`.
- [[js-es6-classes-basics]] — Understand modern class syntax built on constructor mechanics.
- [[MOC - JS Objects & Structures]] — Central Map of Content for Objects.
