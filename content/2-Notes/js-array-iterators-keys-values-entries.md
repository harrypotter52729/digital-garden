---
id: 202607261332
title: "Array Iterators and Iteration Protocols: keys, values, entries, and for...of"
aliases:
  - array-iterators
  - iterable-protocol
  - keys-values-entries
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/arrays
  - topic/iterators
date_created: 2026-07-26
mastery_level: 1
---

# Array Iterators and Iteration Protocols: keys, values, entries, and for...of

> **TL;DR:** JavaScript arrays implement the **Iterable Protocol** via `[Symbol.iterator]()`. Array iterator methods (**`keys()`**, **`values()`**, **`entries()`**) return lazy iterator objects. **`for...of`** loops consume these iterators value-by-value until `done: true`, whereas **`for...in`** loops iterate over object property keys (making `for...in` error-prone for arrays).

## 1. The Two Iteration Protocols

Understanding iterators explains why `for...of`, destructuring `[a, b] = arr`, and spread `[...arr]` work seamlessly across Arrays, Strings, Sets, Maps, and Generators.

### The Iterable Protocol
An object is **Iterable** if it implements a zero-argument method whose key is `[Symbol.iterator]()` that returns an iterator object:

```javascript
const iterator = arr[Symbol.iterator]();
```

### The Iterator Protocol
An object is an **Iterator** if it implements a `.next()` method returning an object with two properties:
- `value`: The current iteration payload.
- `done`: `false` while items remain; `true` when iteration finishes.

---

## 2. Array Iterator Utility Methods

JavaScript provides three built-in iterator generators on arrays:

| Method | Iterator Output Value | `for...of` Loop Idiom |
| :--- | :--- | :--- |
| **`arr.keys()`** | Index numbers (`0`, `1`, `2`) | `for (const index of arr.keys())` |
| **`arr.values()`** | Element values (`"A"`, `"B"`) | Default for `for (const val of arr)` |
| **`arr.entries()`** | Tuple pairs `[index, element]` | `for (const [index, val] of arr.entries())` |

---

## 3. `for...of` vs. `for...in` for Arrays

> **Interview Trap:** Why is `for...in` bad for arrays?

- **`for...of`**: Uses the **Iterable Protocol**. Iterates strictly over values produced by the array's iterator. Ignores named non-index properties and **visits holes as `undefined`**.
- **`for...in`**: Uses **Object Enumerable Properties**. Iterates over string property keys (including custom properties like `arr.owner`), skips array holes, and does not guarantee numeric order!

```javascript
const list = ["A", "B"];
list.owner = "Luffy";

for (const val of list) console.log(val); // "A", "B" (Clean value iteration)
for (const key in list) console.log(key); // "0", "1", "owner" (Polluted with object keys!)
```

---

## Canonical Code Example

This script demonstrates manual iterator steps using `.next()`, using `keys()`, `values()`, `entries()`, and comparing `for...of` vs `for...in`:

```javascript
// --- 1. Manual Iterator Consumption via Symbol.iterator ---
const letters = ["X", "Y"];
const manualIterator = letters[Symbol.iterator]();

console.log(manualIterator.next()); // { value: "X", done: false }
console.log(manualIterator.next()); // { value: "Y", done: false }
console.log(manualIterator.next()); // { value: undefined, done: true }

// --- 2. Exploring keys(), values(), and entries() ---
const items = ["Apple", "Banana", "Cherry"];

console.log("\n--- keys() Iterator ---");
for (const key of items.keys()) {
  console.log("Index key:", key, "Type:", typeof key); // 0, 1, 2 (Number type!)
}

console.log("\n--- entries() with Destructuring ---");
for (const [index, value] of items.entries()) {
  console.log(`Index ${index} => ${value}`);
}

// --- 3. Custom Object implementing Symbol.iterator ---
const rangeGenerator = {
  start: 1,
  end: 3,
  *[Symbol.iterator]() {
    for (let i = this.start; i <= this.end; i++) {
      yield i * 10;
    }
  }
};

console.log("\n--- Custom Iterable Object ---");
for (const val of rangeGenerator) {
  console.log("Custom yield:", val); // 10, 20, 30
}

// Spread uses iterators:
console.log("Spread custom iterable:", [...rangeGenerator]); // [10, 20, 30]
```

---

## Related
* [[js-for-of-iterator]] - Core for...of loop mechanics.
* [[js-generators-and-iterators]] - Deep dive into generator functions.
* [[js-for-in-legacy]] - Why for...in is legacy for arrays.
* [[js-array-specialized-objects]] - Array object property keys.
* [[MOC - JS Built-in Objects & Utilities]] - Standard ECMAScript iterator specifications.
