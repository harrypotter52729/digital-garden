---
id: 202607261321
title: Dense vs Sparse Arrays and Empty Slots (Holes)
aliases:
  - sparse-arrays
  - empty-slots-vs-undefined
  - holey-arrays
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/arrays
  - topic/memory
date_created: 2026-07-26
mastery_level: 1
---

# Dense vs Sparse Arrays and Empty Slots (Holes)

> **TL;DR:** A **dense array** has contiguous values stored at every index from `0` to `length - 1`. A **sparse array** contains missing property keys called **empty slots (holes)**. An empty slot is **not** the value `undefined`; it represents an index where no property key exists on the object at all.

## Dense vs. Sparse (Holey) Arrays

- **Dense Array:** Every index position contains an explicit value (e.g., numbers, strings, or even `undefined`). Engines like V8 optimize dense arrays using fast contiguous memory structures (e.g., `PACKED_SMI_ELEMENTS`).
- **Sparse Array:** Created when you jump indexes (e.g., `arr[100] = 5` on an empty array) or use `new Array(3)`. Most index properties do not exist, forcing engine optimizations to downgrade to dictionary lookup mode (`HOLEY_ELEMENTS`).

---

## Empty Slots (Holes) vs. `undefined`

This distinction is crucial for JavaScript interviews and debugging:

```javascript
const a = new Array(3);               // Contains 3 empty slots (holes)
const b = [undefined, undefined, undefined]; // Contains 3 elements storing undefined
```

| Check / Operation | Sparse Array (`new Array(3)`) | `undefined` Array (`[undefined, undefined, undefined]`) |
| :--- | :---: | :---: |
| **Property Exists? (`0 in arr`)** | `false` | **`true`** |
| **Has Own Property? (`arr.hasOwnProperty(0)`)** | `false` | **`true`** |
| **Reading Index (`arr[0]`)** | `undefined` (Fallback) | `undefined` (Stored Value) |
| **Iterated by `map()`, `forEach()`, `filter()`** | **Skipped** | **Visited** |
| **Checked by `includes(undefined)`** | **`true`** (Reads index) | **`true`** (Reads index) |
| **Checked by `indexOf(undefined)`** | **`-1`** (Skips holes) | **`0`** (Matches element) |

---

## Canonical Code Example

Here is a comprehensive script demonstrating the internal behavioral differences between array holes and explicit `undefined` values:

```javascript
// --- 1. Constructing Sparse vs Undefined Arrays ---
const sparseArr = new Array(3); // [<3 empty items>]
const undefArr = [undefined, undefined, undefined];

console.log("Sparse Array Length:", sparseArr.length); // 3
console.log("Undefined Array Length:", undefArr.length); // 3

// --- 2. Checking Property Existence ---
console.log("0 in sparseArr:", 0 in sparseArr); // false (No property key "0")
console.log("0 in undefArr:", 0 in undefArr);   // true (Property "0" exists with value undefined)

console.log("sparseArr.hasOwnProperty(0):", sparseArr.hasOwnProperty(0)); // false
console.log("undefArr.hasOwnProperty(0):", undefArr.hasOwnProperty(0));   // true

// --- 3. Reading Missing Property Fallback ---
// Reading a missing property on any JS object returns undefined
console.log("sparseArr[0]:", sparseArr[0]); // undefined (not because value is undefined, but because property is missing)
console.log("undefArr[0]:", undefArr[0]);   // undefined

// --- 4. Iteration Behavior Difference ---
let sparseCount = 0;
sparseArr.forEach(() => sparseCount++);

let undefCount = 0;
undefArr.forEach(() => undefCount++);

console.log("forEach execution count on holes:", sparseCount); // 0 (Holes are skipped!)
console.log("forEach execution count on undefined:", undefCount); // 3 (Visited!)

// --- 5. Creating Holes via Array Deletion vs Splice ---
const demo = [10, 20, 30];
delete demo[1]; // Creates a HOLE at index 1 without changing length!
console.log("After delete demo[1]:", demo); // [10, <1 empty item>, 30]
console.log("1 in demo:", 1 in demo); // false
console.log("demo.length:", demo.length); // 3
```

---

## Related
* [[js-array-specialized-objects]] - Array object property key storage.
* [[js-array-holes-in-methods]] - How holes behave across iteration, searching, and copying algorithms.
* [[js-array-length-mechanics]] - How highest index determines array length.
* [[MOC - JS Data Types & Memory]] - Memory representations in JavaScript.
* [[MOC - JS Built-in Objects & Utilities]] - Hub for standard JavaScript objects.
