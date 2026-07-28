---
id: 202607281959
title: Temporal Dead Zone (TDZ) Mechanics
aliases:
  - Temporal Dead Zone
  - TDZ javascript
  - tdz hoisting
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/variables
  - topic/execution
date_created: {today}
mastery_level: 1
---

# Temporal Dead Zone (TDZ) Mechanics

> **TL;DR:** The Temporal Dead Zone (TDZ) is the time span between entering a scope frame and the moment a `let` or `const` variable statement is evaluated—accessing the variable during this window triggers an instant `ReferenceError`!

---

## Canonical Code Example

```javascript
/**
 * Demonstrates TDZ behavior with let, const, and default function parameters.
 */

console.log("--- TDZ Scope Demonstration ---");

{
  // TDZ for variable 'message' starts HERE when block scope is entered!
  
  // console.log(message); // CRASH! ReferenceError: Cannot access 'message' before initialization

  let message = "Hello Zettelkasten!"; // TDZ ends HERE!
  console.log("Post-Initialization:", message); // Expected: "Hello Zettelkasten!"
}

// Default Parameter TDZ Trap
function compute(a = b, b = 2) {
  return a + b;
}

try {
  compute();
} catch (err) {
  console.log("Parameter TDZ Error:", err.message); // Expected: Cannot access 'b' before initialization
}
```

---

## Related
- [[js-hoisting]] — Variable hoisting mechanics in JavaScript.
- [[js-var-let-const]] — Differences between var, let, and const.
- [[MOC - JS Variables & Execution]] — Variables MOC.
