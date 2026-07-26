---
id: 202607261323
title: "Array Creation Techniques: [], Array(), Array.of(), and Array.from()"
aliases:
  - array-creation
  - array-constructor-ambiguity
  - array-of-vs-array-from
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/arrays
  - topic/syntax
date_created: 2026-07-26
mastery_level: 1
---

# Array Creation Techniques: [], Array(), Array.of(), and Array.from()

> **TL;DR:** **Array Literal (`[]`)** is the fastest, cleanest, and preferred way to create arrays. The **`Array()` constructor** has a notorious ambiguity (1 numeric arg creates holes, multiple args create elements). **`Array.of()`** removes this ambiguity by treating every argument as an element. **`Array.from()`** converts iterables or array-like objects into real arrays and allows inline mapping.

## Overview of Creation Techniques

JavaScript provides four primary ways to instantiate arrays, each suited for different use cases:

| Method | Single Numeric Argument Behavior | Ambiguous? | Ideal Use Case |
| :--- | :--- | :---: | :--- |
| `[1, 2, 3]` | N/A (Literal values) | **No** | Default choice when elements are known upfront. |
| `Array(5)` / `new Array(5)` | Creates an array of **5 empty slots (holes)** | **YES** | Pre-allocating capacity (rarely needed). |
| `Array.of(5)` | Creates an array containing the single number `[5]` | **No** | Explicitly creating arrays from arguments without constructor traps. |
| `Array.from(source, mapFn)` | Converts `source` (Iterable/Array-like) into array | **No** | Converting `Set`, `Map`, `NodeList`, `String`, or generating ranges. |

---

## 1. The `Array()` Constructor Ambiguity

The `Array()` constructor changes behavior depending on argument count and type:

```javascript
new Array(3);     // [<3 empty items>] -> Creates holes!
new Array("3");   // ["3"]             -> Non-number: creates 1 element!
new Array(1, 2);  // [1, 2]            -> Multiple args: creates elements!
```

Because of this ambiguity, style guides (such as Airbnb JS Style Guide) forbid `new Array()` in favor of array literals or `Array.of()`.

---

## 2. Power of `Array.from()`

`Array.from(arrayLike, mapFn)` is one of the most versatile methods in ECMAScript:
1. **Converts Array-Like Objects:** Any object with a `.length` property (e.g., `{ length: 3 }`, `arguments`, `HTMLCollection`).
2. **Converts Iterables:** `Set`, `Map`, `String`, `Generator`.
3. **Inline Mapping:** Avoids intermediate array allocations by accepting a map callback as the 2nd parameter: `Array.from(source, fn)` is faster than `Array.from(source).map(fn)`.

---

## Canonical Code Example

This script demonstrates all four array creation techniques and common real-world initialization patterns:

```javascript
// --- 1. Array Literal ([]) ---
const literalArr = [1, 2, 3];
console.log("Literal Array:", literalArr); // [1, 2, 3]

// --- 2. Array Constructor Trap vs Array.of ---
const trap1 = new Array(3);
const trap2 = new Array("3");
const fixed = Array.of(3);

console.log("Array(3):", trap1); // [<3 empty items>] (Sparse array!)
console.log("Array('3'):", trap2); // ["3"]
console.log("Array.of(3):", fixed); // [3] (Dense array with value 3)

// --- 3. Array.from with Iterables ---
const stringArr = Array.from("JS");
console.log("From String:", stringArr); // ["J", "S"]

const uniqueSet = new Set([10, 20, 20, 30]);
const setArr = Array.from(uniqueSet);
console.log("From Set:", setArr); // [10, 20, 30]

// --- 4. Generating Ranges and Populating Values with Array.from ---
// Pattern A: Create [0, 1, 2, 3, 4]
const range = Array.from({ length: 5 }, (_, index) => index);
console.log("Generated Range:", range); // [0, 1, 2, 3, 4]

// Pattern B: Create [undefined, undefined, undefined] WITHOUT holes!
const nonHoleyUndef = Array.from({ length: 3 });
console.log("Non-holey undefined array:", nonHoleyUndef); // [undefined, undefined, undefined]
console.log("0 in nonHoleyUndef:", 0 in nonHoleyUndef); // true!
```

---

## Related
* [[js-array-sparse-vs-dense-holes]] - Why `Array(3)` creates holes while `Array.from({length:3})` does not.
* [[js-array-flat-and-flatmap]] - Combining mapping and flattening.
* [[js-iterable-vs-enumerable]] - Understanding array-like vs iterable objects.
* [[MOC - JS Built-in Objects & Utilities]] - Standard constructors and utility methods.
* [[MOC - JS Objects & Structures]] - Object creation patterns.
