---
id: 202606120833
title: Memoization Pattern
aliases:
  - memoization
  - caching-functions
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/performance
date_created: 2026-06-12
mastery_level: 1
---

# Memoization Pattern

> **TL;DR:** Memoization is like a **student keeping a cheat sheet** of difficult math problems they've already solved. When asked the same problem again, they copy the answer directly instead of recalculating it from scratch.

## The Mental Model
Imagine you run a grocery store. A customer walks in and asks: "What is 493 multiplied by 32?"
You take out a calculator, spend 10 seconds computing `15,776`, and tell them the answer.

If 2 seconds later another customer asks: "What is 493 multiplied by 32?", you wouldn't take out the calculator again. You would remember the answer you just computed and say: "It's 15,776." 

In programming, **memoization** is a performance optimization technique where you store the results of expensive function calls inside a cache lookup object. When the function is called with the same arguments, you return the cached result instead of repeating the heavy calculations.

---

## 1. Rules for Memoization
You can only memoize functions that are **Pure**.
- **Pure Function:** A function that, given the same inputs, will *always* return the exact same output, and has no side effects (like API requests, database queries, or updating outer variables).
- If your function queries a database or gets the current time, memoization will break it, because it will return stale cached data instead of real-time updates.

---

## 2. Implementing a Cache with [[js-closures-and-lexical-environment|Closures]]
To memoize a function, we return a wrapper function that holds a private `cache` object in its lexical environment.

1. Convert the input arguments into a string key (e.g., using `[[js-json-parse-and-stringify|JSON.stringify(args)]]`).
2. Check if the key already exists inside the cache.
3. If it exists, return the cached value.
4. If it doesn't, execute the function, save the result to the cache, and return it.

---

## Canonical Code Example

Here is a script implementing a generic `memoize` helper, and comparing performance on a slow Fibonacci calculation:

```javascript
// --- 1. The Generic Memoization Wrapper ---
function memoize(func) {
  const cache = {}; // Private cache object stored in closure

  return function (...args) {
    // Generate a unique string key based on arguments
    const key = JSON.stringify(args);
    
    // Check cache
    if (key in cache) {
      console.log(`[Cache Hit] Returning stored result for key: ${key}`);
      return cache[key];
    }
    
    // Cache miss: compute, store, and return
    console.log(`[Cache Miss] Computing result for key: ${key}`);
    const result = func.apply(this, args);
    cache[key] = result;
    return result;
  };
}


// --- 2. An Expensive Function Example (Fibonacci) ---
const slowFibonacci = (n) => {
  if (n <= 1) return n;
  return slowFibonacci(n - 1) + slowFibonacci(n - 2);
};

// Create a memoized version of the Fibonacci calculator
const memoizedFib = memoize(slowFibonacci);

// Let's test the execution speed
console.time("First Run (n = 35)");
console.log("Result:", memoizedFib(35)); // Computes fresh
console.timeEnd("First Run (n = 35)");

console.time("Second Run (n = 35)");
console.log("Result:", memoizedFib(35)); // Returns instantly from cache!
console.timeEnd("Second Run (n = 35)");
```

---

## Related
* [[js-closures-and-lexical-environment]] - Accessing persistent cache maps in memory.
* [[js-primitive-vs-reference-types]] - Stringifying references to form keys.
* [[MOC - JS Objects & Structures]] - Memory layout of lookup tables.
* [[js-json-parse-and-stringify]] - Serializing arguments to flat strings.
