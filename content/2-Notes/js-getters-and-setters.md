---
id: 202607280811
title: Accessor Properties (Getters and Setters)
aliases:
  - getters and setters
  - accessor properties
  - get set syntax
  - computed properties getters
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/properties
  - topic/encapsulation
date_created: 2026-07-28
mastery_level: 1
---

# Accessor Properties (Getters and Setters)

> **TL;DR:** A getter is like a smart thermostat display that computes the current temperature dynamically whenever you glance at it, while a setter is the control dial that validates your desired temperature setting before adjusting the system. Neither stores the state directly—they provide a controlled, reactive gateway to internal data!

---

## Why This Exists
Standard data properties simply store values without logic. If a developer needs a property value that depends on other dynamic fields (e.g., `fullName` computed from `firstName` and `lastName`), storing `fullName` as a data property leads to stale data whenever `firstName` or `lastName` changes.

Furthermore, assigning values to data properties (`obj.age = -5`) allows invalid data to corrupt objects. **Getters and Setters** allow functions to masquerade as properties, enabling automatic calculation, validation, logging, and lazy evaluation during property reads and writes.

---

## Mental Model
Think of an object with getters and setters as a bank vault teller window. 
- When you read `account.balance`, you don't reach into the vault yourself. The getter function executes behind the glass, counts the money in the backing store (`_balance`), and reports the total to you.
- When you assign `account.balance = 5000`, the setter function executes, inspects the deposit check to make sure it isn't counterfeit or negative, updates the internal backing store, and logs the transaction.

```
PROPERTY READ (user.fullName)
   │
   ▼
[ Getter Function ] ──► Computes (`${this.first} ${this.last}`) ──► Returns "Monkey Luffy"

PROPERTY WRITE (user.age = 25)
   │
   ▼
[ Setter Function ] ──► Validates (val >= 0) ──► Updates Backing Field (this._age = 25)
```

---

## How It Works

1. **Getter Mechanics (`get prop()`):**
   - Binds an object property to a parameterless function.
   - When `obj.prop` is read, the getter function executes automatically with `this` bound to `obj`.
   - The returned value of the function becomes the result of the property evaluation.
2. **Setter Mechanics (`set prop(value)`):**
   - Binds an object property to a function taking exactly one parameter.
   - When `obj.prop = newProp` is executed, the setter function runs automatically receiving `newProp` as its parameter.
3. **Backing Fields Convention:** To store actual data without causing infinite recursion loops, internal state is typically stored in a backing property prefixed with an underscore (e.g., `this._age` or `this._balance`).

---

## Key Characteristics
- **No Parentheses Required:** Accessors are invoked using dot or bracket notation without parentheses (`user.fullName`, not `user.fullName()`).
- **Accessor Descriptor Conversion:** Defining `get` or `set` creates an Accessor Descriptor (`{ get, set, enumerable, configurable }`) rather than a Data Descriptor.
- **Strict Validation:** Setters can throw errors or sanitize values before assignment.

---

## Common Mistakes

### Mistake 1: Infinite recursion inside setters
Writing `this.age = value` inside `set age(val)` causes the setter to invoke itself recursively until a `RangeError: Maximum call stack size exceeded` occurs!

```javascript
const user = {
  // CRASH! Recursive loop: assigning 'this.age' triggers 'set age()' infinitely!
  set age(val) {
    this.age = val; 
  }
};
// user.age = 25; // Stack Overflow!

// CORRECT FIX: Use a distinct backing field:
const fixedUser = {
  _age: 0,
  set age(val) {
    this._age = val; // Safe backing property assignment
  }
};
```

### Mistake 2: Calling a getter as a function
Attempting to call `user.fullName()` when `fullName` is defined as a getter throws a `TypeError: user.fullName is not a function`.

---

## Best Practices
- **Use getters for computed properties** where values depend dynamically on other internal state.
- **Use setters for data validation, side effects, and sanitization** during assignment.
- **Keep getters fast and side-effect free.** Do not execute heavy database queries or network requests inside a getter unless using memoization.

---

## Comparison Table

| Attribute | Normal Data Property | Accessor Property (Getter/Setter) | Method Function (`obj.fn()`) |
| :--- | :--- | :--- | :--- |
| **Invocation Syntax** | `obj.prop` | `obj.prop` | `obj.prop()` |
| **Storage Location** | Holds value in memory slot | Executes dynamic function logic | Holds function object reference |
| **Accepts Parameters**| No | **Setters accept 1 value** | Accepts arbitrary parameters |
| **Validation Ability**| None | **Built-in via Setter** | Manual inside method |
| **Stale Data Risk** | High for derived values | **Zero (Always fresh)** | Zero (Always fresh) |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates getters, setters, data validation, computed properties,
 * backing fields, and lazy memoization.
 */

class UserProfile {
  constructor(firstName, lastName, age) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.age = age; // Triggers setter validation on initialization!
    this._cachedReport = null;
  }

  // 1. Getter for Computed State
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  // 2. Both Getter and Setter for Backing Field with Validation
  get age() {
    return this._age;
  }

  set age(value) {
    if (typeof value !== "number" || value < 0 || value > 150) {
      throw new TypeError("Invalid age supplied. Must be a number between 0 and 150.");
    }
    this._age = value;
  }

  // 3. Lazy Evaluation / Memoized Getter Pattern
  get heavyAuditReport() {
    if (!this._cachedReport) {
      console.log("Computing expensive audit report...");
      this._cachedReport = `Audit Summary for ${this.fullName} at ${new Date().toISOString()}`;
    }
    return this._cachedReport;
  }
}

// Execution & Verification
const user = new UserProfile("Monkey", "Luffy", 19);

// Reading computed getter (looks like a property)
console.log("Full Name:", user.fullName); // Expected: "Monkey Luffy"

// Updating backing field via validated setter
user.age = 20;
console.log("Updated Age:", user.age); // Expected: 20

// Testing Validation Exception
try {
  user.age = -5; // Throws Exception
} catch (err) {
  console.log("Caught Validation Error:", err.message); 
  // Expected: "Invalid age supplied. Must be a number between 0 and 150."
}

// Lazy Evaluation Verification
console.log("First Read:", user.heavyAuditReport);  // Logs "Computing..." & returns report
console.log("Second Read:", user.heavyAuditReport); // Returns cached report immediately!
```

---

## Key Takeaways
- Getters (`get prop()`) execute logic on read operations to deliver fresh computed properties without parentheses.
- Setters (`set prop(val)`) execute logic on assignment operations to validate or transform inputs.
- Always assign to a separate backing field (e.g., `this._prop`) inside setters to prevent infinite recursion stack overflows.
- Accessor descriptors replace `value` and `writable` attributes with `get` and `set` functions.

---

## Related
- [[js-property-descriptors]] — Full architectural breakdown of Data vs Accessor Descriptors.
- [[js-define-property]] — Defining getters and setters via `Object.defineProperty()`.
- [[js-proxy-api-foundations]] — Intercepting property access beyond basic object accessors.
- [[MOC - JS Objects & Structures]] — Central Map of Content for Objects.
