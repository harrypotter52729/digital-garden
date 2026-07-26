---
id: 202607261331
title: "Modern ES2023 Non-Mutating Array Methods: toSorted, toReversed, toSpliced, and with"
aliases:
  - es2023-array-methods
  - immutable-array-methods
  - tosorted-toreversed-tospliced-with
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/arrays
  - topic/immutability
date_created: 2026-07-26
mastery_level: 1
---

# Modern ES2023 Non-Mutating Array Methods: toSorted, toReversed, toSpliced, and with

> **TL;DR:** ECMAScript 2023 introduced four non-mutating counterparts to traditional destructive array operations: **`toSorted()`**, **`toReversed()`**, **`toSpliced()`**, and **`with()`**. These return a **shallow copy** with modifications applied, leaving the original array completely untouched.

## Mutating Legacy vs. Non-Mutating ES2023

State management in frameworks like React and Redux relies on **immutability** to detect state changes. Historically, developers had to create manual copies using spread `[...arr]` before mutating:

```javascript
// Legacy workaround for immutable sort:
const sorted = [...original].sort();

// Modern ES2023 direct solution:
const sorted = original.toSorted();
```

| Legacy Mutating Method | ES2023 Immutable Alternative | Return Value | Mutates Original? |
| :--- | :--- | :--- | :---: |
| `arr.sort(compareFn)` | **`arr.toSorted(compareFn)`** | New sorted shallow copy | **No** |
| `arr.reverse()` | **`arr.toReversed()`** | New reversed shallow copy | **No** |
| `arr.splice(start, count, ...items)` | **`arr.toSpliced(start, count, ...items)`**| New spliced shallow copy | **No** |
| `arr[index] = newValue` | **`arr.with(index, newValue)`** | New shallow copy with updated item | **No** |

---

## 1. Array.prototype.with(index, value)

`with(index, value)` replaces an element at a given index without mutating the array. It also supports **negative indexing** (e.g., `with(-1, "newLast")` replaces the last item).

```javascript
const items = ["A", "B", "C"];
const updated = items.with(1, "X"); // ["A", "X", "C"]
// items remains ["A", "B", "C"]
```

---

## 2. Array.prototype.toSpliced() vs splice()

A major difference between `splice()` and `toSpliced()` is their return value:
- **`splice()`**: Returns an array of **removed elements**.
- **`toSpliced()`**: Returns the **new modified copy of the array**.

---

## Canonical Code Example

This script demonstrates immutable sorting, reversing, splicing, and element replacement using ES2023 methods:

```javascript
// --- 1. Immutable Reversing (toReversed) ---
const originalNums = [1, 2, 3];
const reversedNums = originalNums.toReversed();

console.log("Original after toReversed:", originalNums); // [1, 2, 3] (Untouched!)
console.log("Reversed Copy:", reversedNums);           // [3, 2, 1]

// --- 2. Immutable Sorting (toSorted) ---
const scores = [40, 10, 100, 5];
const sortedScores = scores.toSorted((a, b) => a - b);

console.log("Original Scores:", scores);             // [40, 10, 100, 5]
console.log("Sorted Scores Copy:", sortedScores);   // [5, 10, 40, 100]

// --- 3. Immutable Splicing (toSpliced) ---
const fruits = ["Apple", "Banana", "Kiwi"];
// Insert "Orange" at index 1 without mutating fruits
const newFruits = fruits.toSpliced(1, 0, "Orange");

console.log("Original Fruits:", fruits);     // ["Apple", "Banana", "Kiwi"]
console.log("toSpliced Result:", newFruits); // ["Apple", "Orange", "Banana", "Kiwi"]

// --- 4. Immutable Index Replacement (with) ---
const team = ["Alice", "Bob", "Charlie"];
const updatedTeam = team.with(1, "Robert").with(-1, "Charles");

console.log("Original Team:", team);           // ["Alice", "Bob", "Charlie"]
console.log("Updated Team Copy:", updatedTeam); // ["Alice", "Robert", "Charles"]
```

---

## Related
* [[js-array-push-pop-shift-unshift-splice]] - Mutating array operations.
* [[js-array-methods-mutating-vs-non-mutating]] - Complete mutating vs non-mutating summary.
* [[js-shallow-vs-deep-copy]] - Understanding shallow immutability limits.
* [[MOC - JS Modern Syntax & Operators]] - ES2023 language additions.
* [[MOC - JS Objects & Structures]] - Immutability patterns in state management.
