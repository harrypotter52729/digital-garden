---
id: 202607280833
title: "Explicit Binding: call(), apply(), and bind()"
aliases:
  - explicit binding
  - call apply bind
  - Function.prototype.call
  - Function.prototype.apply
  - Function.prototype.bind
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/this-keyword
  - topic/functions
date_created: 2026-07-28
mastery_level: 1
---

# Explicit Binding: `call()`, `apply()`, and `bind()`

> **TL;DR:** If implicit binding is an actor taking whatever spotlight is on stage, **Explicit Binding** is the director grabbing the spotlight with their hands and pointing it at a specific actor! `.call()` and `.apply()` execute the scene immediately with a forced target, whereas `.bind()` creates a permanent locked duplicate script that always uses that actor!

---

## Why This Exists
Implicit `this` binding depends entirely on having a caller object preceding a dot at the call site (`obj.method()`). However, developers frequently need to invoke a function using a specific object context when no dot relationship exists—such as borrowing methods from another object, invoking generic utility functions on custom data, or passing methods as callbacks without losing `this`.

JavaScript provides `.call()`, `.apply()`, and `.bind()` on `Function.prototype` to explicitly specify what object should be bound to `this`.

---

## Mental Model
Think of a rental car:
- **`call(thisArg, arg1, arg2)`**: You rent a specific car (`thisArg`), drive it **immediately**, and hand the keys to passengers one by one (`arg1, arg2`).
- **`apply(thisArg, [argsArray])`**: You rent a specific car (`thisArg`), drive it **immediately**, but load all passengers at once inside a luggage bag (`[argsArray]`).
- **`bind(thisArg, arg1)`**: You sign a long-term lease for a specific car (`thisArg`). It does **not** drive now; it hands you a new key fob (a bound function) that always opens that car whenever pressed later.

```
                  ┌────────────────────────────────────────┐
                  │           EXPLICIT BINDING             │
                  └───────────────────┬────────────────────┘
                                      │
           ┌──────────────────────────┼──────────────────────────┐
           ▼                          ▼                          ▼
   fn.call(thisArg, a, b)    fn.apply(thisArg, [a, b])     fn.bind(thisArg, a)
   - Executes IMMEDIATELY    - Executes IMMEDIATELY        - Does NOT execute now
   - Comma-separated args    - Array of args               - Returns NEW bound function
```

---

## How It Works

### 1. `fn.call(thisArg, arg1, arg2, ...)`
- **Immediate Execution:** Invokes `fn` immediately.
- Sets `this` inside `fn` to `thisArg`.
- Passes individual arguments following `thisArg` sequentially.

### 2. `fn.apply(thisArg, argsArray)`
- **Immediate Execution:** Invokes `fn` immediately.
- Sets `this` inside `fn` to `thisArg`.
- Spreads elements of `argsArray` into `fn`'s parameters.

### 3. `fn.bind(thisArg, arg1, arg2, ...)`
- **Deferred Execution:** Does **NOT** execute `fn` immediately.
- Returns a **new bound function wrapper** with `this` hardcoded to `thisArg`.
- Supports **Partial Application (Currying)**: Arguments passed to `.bind()` are prepended to future invocation arguments.

---

## Key Characteristics
- **Hard Binding:** A function created via `.bind()` cannot have its `this` context changed by subsequent `.call()`, `.apply()`, or implicit dot calls.
- **Null/Undefined `thisArg` Behavior:** Passing `null` or `undefined` as `thisArg` causes non-strict mode to default to the global object (`window`/`global`), while strict mode retains `null`/`undefined`.

---

## Common Mistakes

### Mistake 1: Expecting `.bind()` to execute the function immediately
`.bind()` returns a new function reference; it does not execute the underlying logic until the returned function is called.

```javascript
function greet() { console.log(this.name); }
const user = { name: "Luffy" };

// WRONG: Nothing printed! .bind() returns a function reference!
userGreet = greet.bind(user); 

// CORRECT: Call the returned bound function
greet.bind(user)(); // Output: "Luffy"
```

### Mistake 2: Calling `.call()` or `.bind()` on Arrow Functions
Arrow functions do not have a `this` binding slot, so explicit binding arguments are ignored.

---

## Best Practices
- **Use `.bind()` when passing methods as callbacks** into event listeners, `setTimeout`, or asynchronous promises.
- **Use `.call()` for immediate execution** when passing a small number of known individual arguments.
- **Use `.apply()` or spread syntax `fn.call(ctx, ...args)`** when arguments are already structured as an array.

---

## Comparison Table

| Method | Executes Immediately? | Argument Format | Returns | Overridden by `new`? |
| :--- | :--- | :--- | :--- | :--- |
| **`fn.call()`** | **YES** | Individual Comma List (`a, b, c`) | Function Return Value | N/A |
| **`fn.apply()`**| **YES** | Array / Array-like (`[a, b, c]`) | Function Return Value | N/A |
| **`fn.bind()`** | **NO** | Individual Comma List (Partial) | **New Bound Function** | **YES (`new` wins)** |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates explicit binding with call(), apply(), and bind(),
 * partial application (currying), method borrowing, and callback binding.
 */
"use strict";

// 1. Target Objects
const userA = { name: "Monkey D. Luffy", role: "Captain" };
const userB = { name: "Roronoa Zoro", role: "Swordsman" };

// Standalone Function needing context
function introduce(greeting, punctuation) {
  return `${greeting}, I am ${this.name}, the ${this.role}${punctuation}`;
}

// 2. Explicit Binding via .call() (Individual Comma Arguments)
const callResult = introduce.call(userA, "Ahoy", "!");
console.log("call() Result:", callResult); 
// Expected: "Ahoy, I am Monkey D. Luffy, the Captain!"

// 3. Explicit Binding via .apply() (Array Arguments)
const applyArgs = ["Greetings", "."];
const applyResult = introduce.apply(userB, applyArgs);
console.log("apply() Result:", applyResult); 
// Expected: "Greetings, I am Roronoa Zoro, the Swordsman."

// 4. Method Borrowing Pattern
const calculator = {
  numbers: [10, 20, 30],
  sum() {
    return this.numbers.reduce((acc, n) => acc + n, 0);
  }
};

const customData = { numbers: [100, 200, 300] };
// Borrowing 'sum' method for customData
const borrowedSum = calculator.sum.call(customData);
console.log("Borrowed Method Sum:", borrowedSum); // Expected: 600

// 5. Explicit Binding & Partial Application via .bind()
const boundIntroduceLuffy = introduce.bind(userA, "Hello"); // Pre-binds 'this' AND 1st arg!

// Calling bound function later with remaining 2nd arg
console.log("bind() Partial App Result:", boundIntroduceLuffy("!!!")); 
// Expected: "Hello, I am Monkey D. Luffy, the Captain!!!"

// 6. Hard-Binding Permanence Verification
const reBoundResult = boundIntroduceLuffy.call(userB, "???"); 
// Attempting to re-bind to userB via .call() fails! 'userA' remains locked!
console.log("Re-bound Attempt Output:", reBoundResult);
// Expected: "Hello, I am Monkey D. Luffy, the Captain???"
```

---

## Key Takeaways
- Explicit binding explicitly defines `this` using `.call()`, `.apply()`, or `.bind()`.
- `.call()` and `.apply()` execute the function immediately; `.call()` takes comma arguments while `.apply()` takes an arguments array.
- `.bind()` returns a new hard-bound function with `this` locked permanently, suitable for callbacks.
- Arrow functions ignore `.call()`, `.apply()`, and `.bind()` because they do not have a `this` slot.

---

## Related
- [[js-this-method-invocation]] — Implicit dot method binding.
- [[js-this-arrow-functions]] — Lexical `this` in arrow functions.
- [[js-this-constructor-invocation]] — How `new` binding overrides `.bind()`.
- [[MOC - JS Objects & Structures]] — Central Map of Content for Objects.
