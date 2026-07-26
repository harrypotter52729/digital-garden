---
id: 202607261330
title: "Array Flattening Mechanics: flat and flatMap"
aliases:
  - array-flat
  - array-flatmap
  - flattening-arrays
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/arrays
  - topic/transformation
date_created: 2026-07-26
mastery_level: 1
---

# Array Flattening Mechanics: flat and flatMap

> **TL;DR:** **`flat(depth)`** recursively flattens nested arrays up to the specified `depth` (defaults to `1`, or `Infinity` for arbitrary depth) while stripping out empty slots. **`flatMap(fn)`** combines `map(fn)` and `flat(1)` in a single pass, avoiding intermediate array allocations.

## 1. Array.prototype.flat(depth)

Nested arrays naturally occur when dealing with grouped REST API payloads, tree structures, or category hierarchies. `flat()` creates a new array with sub-array elements concatenated:

- **Default Depth (`1`):** Removes 1 level of nesting: `[ [1, 2], [3] ]` $\rightarrow$ `[1, 2, 3]`.
- **Custom Depth (`2`, `3`):** Flattens up to $N$ levels: `[1, [2, [3]]]` with `flat(2)` $\rightarrow$ `[1, 2, 3]`.
- **Arbitrary Depth (`Infinity`):** Flattens any level of nesting completely: `arr.flat(Infinity)`.
- **Hole Removal:** `flat()` automatically strips empty slots (`[1, , 3].flat()` $\rightarrow$ `[1, 3]`).

---

## 2. Array.prototype.flatMap(mapFn)

`flatMap()` maps each element using a mapping callback function, then flattens the result by **1 level**:

$$\text{arr.flatMap(fn)} \equiv \text{arr.map(fn).flat(1)}$$

### Why `flatMap()` is Superior to `map().flat()`
1. **Memory Efficiency:** `map().flat()` allocates a temporary intermediate nested array in memory before flattening. `flatMap()` performs mapping and flattening in a single pass.
2. **Filtering / Expanding Items:** 
   - Return a 1-element array `[item]` to keep an item.
   - Return an empty array `[]` to **filter out / remove** an item.
   - Return a multi-element array `[a, b]` to **expand / split** an item into multiple elements.

```javascript
// Expanding sentence into words:
["Hello World", "JS Arrays"].flatMap(s => s.split(" "));
// Output: ["Hello", "World", "JS", "Arrays"]
```

> **Constraint:** `flatMap()` **only flattens depth 1**. If your callback returns arrays nested deeper than 1 level, use `map(fn).flat(depth)`.

---

## Canonical Code Example

This script demonstrates depth-based flattening, `Infinity` depth, hole removal, and `flatMap` item expansion/filtering patterns:

```javascript
// --- 1. Basic flat() and Depth Control ---
const nested = [1, [2, [3, [4]]]];

console.log("flat(1):", nested.flat(1)); // [1, 2, [3, [4]]]
console.log("flat(2):", nested.flat(2)); // [1, 2, 3, [4]]
console.log("flat(Infinity):", nested.flat(Infinity)); // [1, 2, 3, 4]

// --- 2. Hole Stripping with flat() ---
const sparseNested = [10, , [20, , 30]];
console.log("Sparse flat():", sparseNested.flat()); // [10, 20, 30] (Holes removed!)

// --- 3. Real-World API Payload Flattening ---
const users = [
  { name: "Alice", orders: ["Laptop", "Mouse"] },
  { name: "Bob", orders: ["Phone"] }
];

// Extract all orders into a single flat array
const allOrders = users.flatMap(u => u.orders);
console.log("All Orders (flatMap):", allOrders); // ["Laptop", "Mouse", "Phone"]

// --- 4. Using flatMap for Concurrent Filtering and Expanding ---
const numbers = [1, 2, 3, 4];

// Rule: Remove odd numbers ([]), duplicate even numbers ([n, n])
const processed = numbers.flatMap(n => {
  if (n % 2 !== 0) {
    return []; // Empty array removes the element!
  }
  return [n, n * 10]; // Multi-element array expands the element!
});

console.log("Filtered & Expanded via flatMap:", processed); // [2, 20, 4, 40]
```

---

## Related
* [[js-array-foreach-vs-map-filter]] - Understanding standard map and filter semantics.
* [[js-array-reduce-and-reduceright]] - Alternative flattening using reduce.
* [[js-array-holes-in-methods]] - Sparse array hole removal rules.
* [[MOC - JS Modern Syntax & Operators]] - Modern ES2019 array APIs.
* [[MOC - JS Built-in Objects & Utilities]] - Standard array transformation APIs.
