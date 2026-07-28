---
id: 202607281957
title: Object Instantiation via Factory Functions
aliases:
  - factory functions
  - js factory function
  - object factory
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/objects
  - topic/functional-programming
date_created: {today}
mastery_level: 1
---

# Object Instantiation via Factory Functions

> **TL;DR:** A Factory Function is a regular function that creates and returns a fresh object instance every time it is invoked—delivering clean object creation and hard private state via closures without requiring `new` or `this` binding headaches!

---

## Why This Exists
While ES6 `class` and constructor functions rely on `this` binding and prototype linkages, Factory Functions provide a flexible functional alternative. They eliminate `this` context binding errors when passing methods as callbacks and enforce hard encapsulation via lexical closures.

---

## Canonical Code Example

```javascript
/**
 * Factory Function demonstrating closure-based encapsulation and method stability.
 */

function createCounter(initialCount = 0) {
  // Hard private state enclosed by lexical environment
  let count = initialCount;

  return {
    increment() {
      count++;
      return count;
    },
    decrement() {
      count--;
      return count;
    },
    get count() {
      return count;
    }
  };
}

const counterA = createCounter(10);
const counterB = createCounter(100);

counterA.increment();
console.log("Counter A:", counterA.count); // Expected: 11
console.log("Counter B:", counterB.count); // Expected: 100 (Independent state!)

// Method reference safety (No 'this' unbinding gotcha!)
const detachedIncrement = counterA.increment;
detachedIncrement();
console.log("Counter A after detached call:", counterA.count); // Expected: 12
```

---

## Related
- [[js-closures-and-lexical-environment]] — Lexical scope powering factory privacy.
- [[js-composition-vs-inheritance]] — Combining factory functions for composition.
- [[MOC - JS Advanced Functions & Performance]] — Advanced Functions MOC.
