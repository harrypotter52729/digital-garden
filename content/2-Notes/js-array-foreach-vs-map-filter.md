---
id: 202607261328
title: "Array Iteration Semantics: forEach vs map vs filter"
aliases:
  - foreach-vs-map
  - map-vs-filter
  - array-functional-iteration
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/arrays
  - topic/functional-programming
date_created: 2026-07-26
mastery_level: 1
---

# Array Iteration Semantics: forEach vs map vs filter

> **TL;DR:** **`forEach()`** is for **side effects** (logging, DOM updates); it **always returns `undefined`** and cannot be chained. **`map()`** is for **1-to-1 data transformation**; it returns a new array of the exact same length. **`filter()`** is for **selection**; it returns a new array containing only elements where the callback returns a truthy value.

## Comparing Core Iteration Methods

Understanding the specific job of each functional method prevents subtle bugs in React state updates, API processing, and data transformations:

| Method | Core Purpose | Return Value | Output Array Length | Callback Return Value Used For? |
| :--- | :--- | :--- | :---: | :--- |
| **`forEach(fn)`** | Side effects (Doing) | `undefined` | N/A | **Ignored** |
| **`map(fn)`** | Transformation (Mapping) | **New Array** | **Same as input** ($N \rightarrow N$) | **Becomes new element** |
| **`filter(fn)`** | Selection (Filtering) | **New Array** | **$\le$ input length** ($N \rightarrow \le N$) | **Truthiness check** (Keep/Discard) |

---

## 1. Common Pitfall: `forEach()` Returns `undefined`

Attempting to assign the result of `forEach()` or use it in JSX results in `undefined`:

```javascript
// WRONG:
const doubled = [1, 2, 3].forEach(x => x * 2); 
console.log(doubled); // undefined! (forEach ignores callback returns)

// CORRECT:
const doubled = [1, 2, 3].map(x => x * 2);
console.log(doubled); // [2, 4, 6]
```

---

## 2. Common Pitfall: `map()` vs `filter()` Callback Returns

- **`map()` stores the callback return value as the new element:** Returning `true` inside `map()` creates an array of booleans (`[true, true]`), not filtered items!
- **`filter()` uses the callback return value as a boolean gate:** If callback returns `true`, the **original element** is kept in the output array.

```javascript
const nums = [1, 2, 3];

// Goal: Keep even numbers
nums.map(x => x % 2 === 0);    // [false, true, false]  (Transformed to booleans!)
nums.filter(x => x % 2 === 0); // [2]                  (Filtered elements!)
```

---

## 3. Early Termination Constraints

You **cannot** use `break` or `continue` inside `forEach()`, `map()`, or `filter()`. If you need early termination (short-circuiting), use:
- `for...of` loop (allows `break` and `continue`)
- `some()` or `every()`
- `find()` or `findIndex()`

---

## Canonical Code Example

This script demonstrates `forEach` side-effects, `map` transformations, `filter` selection, method chaining pipelines, and object literal return syntax:

```javascript
// --- 1. forEach() for Side-Effects ---
const logs = [];
[1, 2, 3].forEach(val => {
  logs.push(`Item ${val}`);
});
console.log("forEach side effect:", logs); // ["Item 1", "Item 2", "Item 3"]

// --- 2. map() 1-to-1 Transformation ---
const prices = [100, 200, 300];
const discounted = prices.map(price => price * 0.9);
console.log("map() discounted prices:", discounted); // [90, 180, 270]

// Arrow function implicit object return syntax (wrap {} in parentheses!)
const users = ["Alice", "Bob"];
const userObjects = users.map(name => ({ username: name, active: true }));
console.log("map() object return:", userObjects);

// --- 3. filter() Selection & Truthy Idiom ---
const rawInput = ["Hello", "", null, "World", undefined, 0, 42];

// Idiom: filter(Boolean) cleans out falsy values
const cleanData = rawInput.filter(Boolean);
console.log("filter(Boolean) cleaned output:", cleanData); // ["Hello", "World", 42]

// --- 4. The Functional Chaining Pipeline (filter -> map) ---
const dataset = [
  { id: 1, name: "Task 1", completed: true },
  { id: 2, name: "Task 2", completed: false },
  { id: 3, name: "Task 3", completed: true }
];

// Pipeline: Extract names of completed tasks only
const completedTaskNames = dataset
  .filter(task => task.completed)
  .map(task => task.name);

console.log("Completed Task Names Pipeline:", completedTaskNames); // ["Task 1", "Task 3"]
```

---

## Related
* [[js-map-filter-reduce]] - Combined functional programming paradigms.
* [[js-array-reduce-and-reduceright]] - Aggregating values using reduce.
* [[js-array-holes-in-methods]] - How forEach, map, and filter skip array holes.
* [[MOC - JS Advanced Functions & Performance]] - Functional programming patterns in JS.
* [[MOC - JS Modern Syntax & Operators]] - Arrow function syntax and expressions.
