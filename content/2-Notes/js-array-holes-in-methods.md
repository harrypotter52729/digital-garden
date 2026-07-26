---
id: 202607261326
title: Hole Handling Behavior Across Array Methods
aliases:
  - array-holes-behavior
  - sparse-array-method-traps
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/arrays
  - topic/edge-cases
date_created: 2026-07-26
mastery_level: 1
---

# Hole Handling Behavior Across Array Methods

> **TL;DR:** JavaScript array methods handle empty slots (holes) in **two fundamentally different ways**. Older ES5 iteration methods (`forEach`, `map`, `filter`, `reduce`, `indexOf`) **skip holes entirely** by performing an internal `i in array` check. Modern ES6+ methods (`includes`, `find`, `findIndex`, `findLast`, `flat`, `Array.from`) **visit holes**, treating missing indices as `undefined`.

## Two Categories of Method Algorithms

The ECMAScript specification dictates how array methods loop through sparse arrays:

### Category 1: Skip Holes (Property Check Methods)
These methods internally check `if (i in array)` before invoking callbacks or checking values:
- **`forEach()`**: Callback is **never** executed for holes.
- **`map()`**: Callback is skipped; the resulting array preserves the hole at that exact position.
- **`filter()`**: Holes are skipped and omitted from the result array.
- **`reduce()`**: Holes are skipped.
- **`indexOf(value)`**: Performs `!(i in array)` check; skips holes entirely. `indexOf(undefined)` on a hole returns `-1`.

### Category 2: Visit Holes as `undefined` (Index Reading Methods)
These methods read values directly via `array[i]`. Reading a missing property returns `undefined`:
- **`includes(value)`**: Reads index value. `includes(undefined)` on a hole returns **`true`**!
- **`find(predicate)`**: Callback visits the hole receiving `undefined` as element.
- **`findIndex(predicate)`**: Callback visits the hole receiving `undefined` as element.
- **`flat()`**: Flattens nested arrays while removing holes (`[1, , 3].flat()` becomes `[1, 3]`).
- **`Array.from()`**: Converts holes into explicit `undefined` values (`Array.from(new Array(2))` becomes `[undefined, undefined]`).

---

## Hole Behavior Cheat Sheet Matrix

Given sparse array: `const a = [1, , 3];`

| Method Call | Skips Hole? | Callback Executed for Hole? | Output Result |
| :--- | :---: | :---: | :--- |
| `a.forEach(fn)` | **Yes** | No | Executes callback for indices `0` and `2` only |
| `a.map(x => 1)` | **Yes** | No | `[1, <1 empty item>, 1]` (Hole remains a hole!) |
| `a.filter(Boolean)` | **Yes** | No | `[1, 3]` (Hole is discarded) |
| `a.indexOf(undefined)` | **Yes** | N/A | **`-1`** (Hole skipped) |
| `a.includes(undefined)`| **No** | N/A | **`true`** (Hole read as `undefined`) |
| `a.find(x => x === undefined)` | **No** | **Yes** | **`undefined`** (Callback runs, match found!) |

---

## Canonical Code Example

The following script proves the dual behavior of array methods on sparse arrays:

```javascript
// --- 1. Constructing a Sparse Array ---
const sparse = [10, , 30]; // Index 1 is a hole

console.log("Length:", sparse.length); // 3
console.log("1 in sparse:", 1 in sparse); // false

// --- 2. Category 1 Demonstration (Skips Holes) ---
console.log("\n--- Category 1: Skipping Holes ---");

// map() preserves holes:
const mapped = sparse.map(x => x * 2);
console.log("map() output:", mapped); // [20, <1 empty item>, 60]
console.log("1 in mapped:", 1 in mapped); // false (Still a hole!)

// filter() removes holes:
const filtered = sparse.filter(() => true);
console.log("filter() output:", filtered); // [10, 30] (Length shrunk to 2!)

// indexOf() skips holes:
console.log("indexOf(undefined):", sparse.indexOf(undefined)); // -1 (Skipped index 1)

// --- 3. Category 2 Demonstration (Treats Holes as undefined) ---
console.log("\n--- Category 2: Visiting Holes as undefined ---");

// includes() reads holes:
console.log("includes(undefined):", sparse.includes(undefined)); // true!

// find() visits holes:
const found = sparse.find((val, idx) => {
  console.log(`find() checking index ${idx}: value = ${val}`);
  return val === undefined;
});
console.log("find() return value:", found); // undefined (Matched at index 1!)

// Array.from fills holes:
const filled = Array.from(sparse);
console.log("Array.from() output:", filled); // [10, undefined, 30]
console.log("1 in filled:", 1 in filled);   // true (Hole converted to real value!)
```

---

## Related
* [[js-array-sparse-vs-dense-holes]] - Foundations of sparse arrays vs undefined.
* [[js-array-searching-includes-vs-indexof-find]] - Detailed search comparisons including NaN.
* [[js-array-foreach-vs-map-filter]] - Functional iteration nuances.
* [[MOC - JS Built-in Objects & Utilities]] - Standard ECMAScript specification behaviors.
* [[MOC - JS Type Conversion & Equality]] - Equality and property resolution rules.
