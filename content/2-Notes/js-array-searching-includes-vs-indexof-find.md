---
id: 202607261327
title: "Searching Array Methods: includes vs indexOf vs find & findIndex"
aliases:
  - array-searching-methods
  - includes-vs-indexof
  - find-vs-filter
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/arrays
  - topic/searching
date_created: 2026-07-26
mastery_level: 1
---

# Searching Array Methods: includes vs indexOf vs find & findIndex

> **TL;DR:** Use **`includes()`** when checking boolean presence of primitives (supports `NaN`). Use **`indexOf()`** / **`lastIndexOf()`** to find the numeric position of primitives. Use **`find()`** / **`findLast()`** to find an **object or element** matching a predicate function. Use **`findIndex()`** / **`findLastIndex()`** to locate an object's index position.

## Decision Matrix: Which Search Method to Use?

Select the right search tool by answering two questions: *What am I searching by?* and *What do I need returned?*

| Goal | Target Type | Method to Use | Return Value | Comparison Algorithm | Stops Early? |
| :--- | :--- | :--- | :--- | :--- | :---: |
| **Check value existence** | Primitive | **`includes(val)`** | `true` / `false` | `SameValueZero` | **Yes** |
| **Find index of primitive** | Primitive | **`indexOf(val)`** | Index or `-1` | Strict Equality (`===`) | **Yes** |
| **Find last index of primitive**| Primitive | **`lastIndexOf(val)`**| Index or `-1` | Strict Equality (`===`) | **Yes** |
| **Find matching object/item** | Condition | **`find(predicate)`** | Element or `undefined`| Callback truthiness | **Yes** |
| **Find index of matching object**| Condition | **`findIndex(predicate)`**| Index or `-1` | Callback truthiness | **Yes** |
| **Find last matching object** | Condition | **`findLast(predicate)`**| Element or `undefined`| Callback truthiness | **Yes** |

---

## 1. `includes()` vs `indexOf()`: The `NaN` and Object Pitfalls

### The `NaN` Comparison Trick
- **`indexOf(NaN)`**: Uses strict equality (`===`). In JavaScript, `NaN === NaN` is `false`, so `indexOf(NaN)` always returns **`-1`**.
- **`includes(NaN)`**: Uses `SameValueZero` algorithm, which treats `NaN` as equal to `NaN`, returning **`true`**.

```javascript
[NaN].indexOf(NaN);  // -1   (Strict equality fails!)
[NaN].includes(NaN); // true (SameValueZero succeeds!)
```

### Searching Objects by Reference
Neither `includes()` nor `indexOf()` can search array objects by structural comparison (`{id: 1} === {id: 1}` is `false`). You **must** use `find()` or `findIndex()` with a callback:

```javascript
const users = [{ id: 1 }];

users.includes({ id: 1 }); // false (Different object reference!)
users.find(u => u.id === 1); // { id: 1 } (Correct!)
```

---

## 2. Performance: `find()` vs `filter()`

A common production anti-pattern is using `filter()[0]` to find a single object:

```javascript
// BAD (Scans entire array and allocates new array):
const user = users.filter(u => u.id === 42)[0];

// GOOD (Stops scanning on 1st match, zero allocation):
const user = users.find(u => u.id === 42);
```

---

## Canonical Code Example

This script demonstrates searching primitives, `NaN` handling, object searching by reference, and reverse search methods:

```javascript
// --- 1. Primitive Searching & NaN Handling ---
const numbers = [10, 20, NaN, 30];

console.log("includes(20):", numbers.includes(20)); // true
console.log("indexOf(20):", numbers.indexOf(20));   // 1

console.log("includes(NaN):", numbers.includes(NaN)); // true (SameValueZero)
console.log("indexOf(NaN):", numbers.indexOf(NaN));   // -1 (Strict equality === fails!)

// --- 2. Object Searching (Reference Trap) ---
const user1 = { id: 101, name: "Alice" };
const user2 = { id: 102, name: "Bob" };
const userList = [user1, user2];

// Direct reference search works with indexOf/includes:
console.log("indexOf(user1):", userList.indexOf(user1)); // 0

// Literal object search FAILS:
console.log("indexOf literal:", userList.indexOf({ id: 101, name: "Alice" })); // -1

// Correct object search using find() and findIndex():
const foundUser = userList.find(u => u.id === 101);
console.log("find() object:", foundUser); // { id: 101, name: "Alice" }

const foundIdx = userList.findIndex(u => u.id === 102);
console.log("findIndex():", foundIdx); // 1

// --- 3. Reverse Searching with findLast / findLastIndex (ES2023) ---
const logs = [
  { status: "ERROR", msg: "Timeout" },
  { status: "OK", msg: "Success" },
  { status: "ERROR", msg: "Database Down" }
];

const lastError = logs.findLast(l => l.status === "ERROR");
console.log("Last Error:", lastError); // { status: "ERROR", msg: "Database Down" }

const lastErrorIdx = logs.findLastIndex(l => l.status === "ERROR");
console.log("Last Error Index:", lastErrorIdx); // 2
```

---

## Related
* [[js-array-holes-in-methods]] - How searching methods handle sparse array holes.
* [[js-strict-vs-loose-equality]] - Difference between `===` and `SameValueZero`.
* [[js-array-foreach-vs-map-filter]] - Comparing find vs filter performance.
* [[MOC - JS Built-in Objects & Utilities]] - Hub for standard search APIs.
* [[MOC - JS Type Conversion & Equality]] - Equality comparison algorithms in JS.
