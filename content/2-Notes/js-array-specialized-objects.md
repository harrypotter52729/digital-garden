---
id: 202607261320
title: Arrays as Specialized Objects
aliases:
  - array-object-model
  - arrays-are-objects
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/arrays
  - topic/objects
date_created: 2026-07-26
mastery_level: 1
---

# Arrays as Specialized Objects

> **TL;DR:** In JavaScript, an array is not a primitive fixed memory block like in C or Java; it is a **specialized object** with extra internal rules. Array indexes are simply string property keys (e.g., `"0"`, `"1"`) managed alongside a dynamic `length` property.

## The Mental Model: Object Under the Hood

Many developers assume `[ "A", "B" ]` is stored as a contiguous memory block of items. In reality, JavaScript engine represents it as an object with integer-like string keys:

```javascript
// What you write:
const arr = ["A", "B"];

// How JavaScript views it conceptually:
{
  "0": "A",
  "1": "B",
  "length": 2
}
```

Because arrays inherit from `Array.prototype` (which inherits from `Object.prototype`), calling `typeof []` returns `"object"`.

---

## 1. Array Indexing vs Custom Named Properties

Since arrays are objects, you can assign arbitrary non-numeric properties to them (e.g., `arr.owner = "Luffy"`). However:
- **Array Indexes:** Non-negative integers strictly less than $2^{32} - 1$. Updates to these indexes automatically adjust the `length` property.
- **Custom Properties:** Named keys like `arr.owner` or `arr["foo"]`. These exist on the object dictionary but **do not affect `length`** and are ignored by standard array iteration methods like `map()` or `forEach()`.

| Property Type | Example Key | Affects `length`? | Traversed by `for...of`? | Traversed by `for...in`? |
| :--- | :--- | :---: | :---: | :---: |
| Valid Index | `"0"`, `"1"`, `2` | **Yes** | **Yes** | **Yes** |
| Custom Property | `"owner"`, `"-1"`, `"1.5"` | No | No | **Yes** (Enumerable) |

---

## 2. Detecting Arrays Correctly

Because `typeof []` evaluates to `"object"`, using `typeof` is useless for array detection. Always use `Array.isArray()`:

```javascript
typeof [];             // "object"  (Unhelpful)
Array.isArray([]);     // true      (Reliable)
```

---

## Canonical Code Example

The following script demonstrates array object properties, custom property assignment, and reliable array detection:

```javascript
// --- 1. Array Object Property Behavior ---
const fruits = ["Apple", "Banana"];

// Adding a custom named property
fruits.owner = "Hemanth";
fruits["category"] = "Produce";

console.log("Array Elements:", fruits);          // ["Apple", "Banana"]
console.log("Custom Property (owner):", fruits.owner); // "Hemanth"
console.log("Array Length:", fruits.length);      // 2 (Custom properties do not increment length!)

// --- 2. Index Keys vs String Keys ---
console.log("Access via Number:", fruits[0]);    // "Apple"
console.log("Access via String:", fruits["0"]);  // "Apple" (Under the hood, keys are strings!)

// --- 3. Correct Type Checking ---
console.log("typeof fruits:", typeof fruits);        // "object"
console.log("Array.isArray:", Array.isArray(fruits)); // true

// --- 4. Iteration Differences ---
console.log("for...of (values only):");
for (const item of fruits) {
  console.log(" ->", item); // Only prints "Apple" and "Banana"
}

console.log("for...in (enumerable keys):");
for (const key in fruits) {
  console.log(" -> key:", key); // Prints "0", "1", "owner", "category"
}
```

---

## Related
* [[js-array-sparse-vs-dense-holes]] - How object properties create holes in sparse arrays.
* [[js-array-length-mechanics]] - Deep dive into how the length property tracks numeric indexes.
* [[js-primitive-vs-reference-types]] - Understanding mutable heap references.
* [[MOC - JS Objects & Structures]] - Hub for JavaScript object mechanics.
* [[MOC - JavaScript Core]] - Core language primitives and execution models.
