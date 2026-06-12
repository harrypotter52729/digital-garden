---
id: 202606120832
title: Function Currying
aliases:
  - currying
  - functional-currying
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/functions
date_created: 2026-06-12
mastery_level: 1
---

# Function Currying

> **TL;DR:** Currying is like a **fast-food assembly line** where each worker adds one specific ingredient before passing the tray. Instead of a function taking all its arguments at once, it takes them one by one, returning a new function at each step.

## The Mental Model
Imagine you want to buy a custom sandwich:
- **Normal Function:** You order: "Give me a sandwich with wheat bread, turkey, and Swiss cheese." The worker hands you the completed sandwich all at once: `makeSandwich(bread, meat, cheese)`.
- **Curried Function:** You walk down the assembly line:
  1. The first worker takes wheat bread and hands you a tray: `makeSandwich("wheat")`.
  2. The second worker adds turkey and hands it back: `addMeat("turkey")`.
  3. The third worker adds Swiss cheese and completes the order: `addCheese("swiss")`.

This translates to calling the function like: `makeSandwich("wheat")("turkey")("swiss")`.

---

## 1. Why Curry? (Partial Application)
Currying is powerful because it allows **Partial Application**. You can configure a generic function with some parameters early, save that configuration, and use it with different parameters later.

For example, you can build a customized logger that has the `"DEBUG"` or `"ERROR"` tags pre-applied, and then call it with different error messages later.

---

## 2. Basic vs. Advanced Currying

### Basic Currying (Using [[js-closures-and-lexical-environment|Closures]])
We write nested functions returning other functions. [[js-arrow-functions-and-lexical-this|Arrow functions]] make this very short:
```javascript
const add = a => b => a + b;
const addFive = add(5); // Locks in 'a' as 5
console.log(addFive(3)); // 8
```

### Advanced Auto-Currying
What if you want a utility function `curry` that can take a normal function and turn it into a curried version automatically?
We can write a wrapper that checks if the number of arguments provided so far is equal to the expected parameters of the original function (`originalFunc.length`). If not, it keeps returning a new function.

---

## Canonical Code Example

Here is a practical look at currying a database query log helper, and writing a generic `curry` utility:

```javascript
// --- 1. Practical Example: Configured Logger ---
// Standard log function: log(importance, message)
const log = importance => message => {
  const time = new Date().toLocaleTimeString();
  console.log(`[${time}] [${importance.toUpperCase()}]: ${message}`);
};

// Partial Application: Lock down the importance levels
const logInfo = log("info");
const logError = log("error");

// Use them later with only the message parameter
logInfo("Server successfully started on port 3000.");
logError("Database connection timed out!");


// --- 2. Advanced: The Auto-Curry Helper ---
// A generic wrapper function that turns a multi-argument function into a curried one
function curry(originalFunc) {
  return function curried(...args) {
    // Check if we have gathered enough arguments to execute the original function
    if (args.length >= originalFunc.length) {
      return originalFunc.apply(this, args);
    } else {
      // Otherwise, return a nested function to gather the next set of arguments
      return function (...nextArgs) {
        return curried.apply(this, [...args, ...nextArgs]);
      };
    }
  };
}

// Example usage of auto-curry:
const multiply = (x, y, z) => x * y * z;
const curriedMultiply = curry(multiply);

console.log(curriedMultiply(2)(3)(4)); // 24
console.log(curriedMultiply(2, 3)(4)); // 24 (Also accepts grouped arguments!)
```

---

## Related
* [[js-closures-and-lexical-environment]] - Retaining arguments inside closure bindings.
* [[js-higher-order-functions-and-callbacks]] - Functions returning other nested functions.
* [[js-arrow-functions-and-lexical-this]] - Short arrows enabling curried chains.
