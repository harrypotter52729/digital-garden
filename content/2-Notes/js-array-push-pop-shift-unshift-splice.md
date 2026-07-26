---
id: 202607261325
title: "Adding & Removing Array Elements: push, pop, shift, unshift, and splice"
aliases:
  - array-mutation-methods
  - push-pop-shift-unshift-splice
  - array-element-manipulation
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/arrays
  - topic/performance
date_created: 2026-07-26
mastery_level: 1
---

# Adding & Removing Array Elements: push, pop, shift, unshift, and splice

> **TL;DR:** All five methods (**`push`**, **`pop`**, **`shift`**, **`unshift`**, **`splice`**) **mutate the original array**. `push()` and `pop()` operate on the end in **$O(1)$ amortized time**. `shift()` and `unshift()` operate on the front in **$O(n)$ time** because every remaining element must shift index positions in memory. `splice()` is a Swiss Army knife that can insert, delete, or replace elements at any index.

## Method Comparison & Return Values

A frequent interview trap involves forgetting what each mutating method returns:

| Method | Operation Location | Return Value | Time Complexity | Mutates Original? |
| :--- | :--- | :--- | :---: | :---: |
| **`push(...items)`** | Appends to **End** | **New `length`** (number) | $O(1)$ amortized | **Yes** |
| **`pop()`** | Removes from **End** | **Removed element** (or `undefined` if empty) | $O(1)$ | **Yes** |
| **`unshift(...items)`**| Inserts at **Start** | **New `length`** (number) | $O(n)$ | **Yes** |
| **`shift()`** | Removes from **Start** | **Removed element** (or `undefined` if empty) | $O(n)$ | **Yes** |
| **`splice(start, count, ...items)`** | Anywhere | **Array of removed elements** | $O(n)$ worst case | **Yes** |

> **Memory Trick:** 
> - **Adding methods (`push`, `unshift`):** Return the **new length**.
> - **Removing methods (`pop`, `shift`):** Return the **removed element**.

---

## 1. Why `push()` is Fast and `shift()` is Slow

- **`push()` / `pop()` ($O(1)$):** Only the index at `length` or `length - 1` is modified. Existing elements at indices `0..N-2` stay untouched in memory.
- **`shift()` / `unshift()` ($O(n)$):** When element `0` is removed or added, every single subsequent element $k$ must be re-indexed to $k-1$ or $k+1$. On a 1,000,000 element array, `shift()` performs 999,999 memory write updates!

```javascript
// AVOID in high-performance queues:
while (queue.length) {
  queue.shift(); // O(n) per iteration -> Total runtime O(n^2)!
}
```

---

## 2. Splice Mechanics: `splice(start, deleteCount, ...items)`

`splice()` performs surgical modifications anywhere in the array:
- `arr.splice(1, 2)` $\rightarrow$ Starts at index `1`, removes `2` elements.
- `arr.splice(1, 0, "A", "B")` $\rightarrow$ Starts at index `1`, deletes `0` elements, inserts `"A"` and `"B"`.
- `arr.splice(1, 1, "X")` $\rightarrow$ Replaces element at index `1` with `"X"`.

---

## Canonical Code Example

This script demonstrates execution mechanics, return values, and index movement across all five mutating methods:

```javascript
// --- 1. push() and pop() Return Values ---
const stack = [10, 20];

const pushResult = stack.push(30, 40); // Returns new length
console.log("push() return value (new length):", pushResult); // 4
console.log("stack after push:", stack); // [10, 20, 30, 40]

const popResult = stack.pop(); // Returns removed element
console.log("pop() return value (removed element):", popResult); // 40
console.log("stack after pop:", stack); // [10, 20, 30]

// --- 2. unshift() and shift() Return Values ---
const queue = ["B", "C"];

const unshiftResult = queue.unshift("A"); // Returns new length
console.log("unshift() return value:", unshiftResult); // 3
console.log("queue after unshift:", queue); // ["A", "B", "C"]

const shiftResult = queue.shift(); // Returns removed element
console.log("shift() return value:", shiftResult); // "A"
console.log("queue after shift:", queue); // ["B", "C"]

// --- 3. splice() Insertion, Deletion, and Replacement ---
const numbers = [1, 2, 5, 6];

// Insert 3 and 4 at index 2 (deleteCount = 0)
const removedInsert = numbers.splice(2, 0, 3, 4);
console.log("splice insert return (removed array):", removedInsert); // []
console.log("numbers after splice insert:", numbers); // [1, 2, 3, 4, 5, 6]

// Remove 2 elements starting at index 4 (remove 5 and 6)
const removedDelete = numbers.splice(4, 2);
console.log("splice delete return:", removedDelete); // [5, 6]
console.log("numbers after splice delete:", numbers); // [1, 2, 3, 4]

// Replace index 1 with 99
numbers.splice(1, 1, 99);
console.log("numbers after splice replace:", numbers); // [1, 99, 3, 4]

// --- 4. Classic Interview Chaining Trap ---
const trapArr = [1, 2];
// What does arr.push(arr.shift()) do?
// Step 1: arr.shift() executes first -> removes 1, returns 1. arr is now [2].
// Step 2: arr.push(1) executes -> appends 1, returns 2 (new length).
const trapResult = trapArr.push(trapArr.shift());
console.log("trapResult:", trapResult); // 2
console.log("trapArr:", trapArr);       // [2, 1] (Rotated left!)
```

---

## Related
* [[js-array-methods-mutating-vs-non-mutating]] - Complete reference of mutating vs immutable array operations.
* [[js-array-modern-immutable-methods]] - Non-mutating ES2023 alternatives like `toSpliced()` and `with()`.
* [[js-array-length-mechanics]] - How push and pop adjust array length.
* [[MOC - JS Built-in Objects & Utilities]] - Standard array method references.
* [[MOC - JS Advanced Functions & Performance]] - Understanding $O(1)$ vs $O(n)$ complexity in JS.
