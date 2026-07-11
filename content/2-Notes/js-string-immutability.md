---
id: 202607112244
title: JavaScript String Immutability
aliases:
  - string-immutability
  - immutable-strings
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
date_created: 2026-07-11
mastery_level: 1
---

# JavaScript String Immutability

> **TL;DR:** String immutability is like a **printed book**. If you want to correct a typo in the book, you cannot magically change the printed ink on the pages. Instead, you must print a **whole new edition** containing the corrected text; the original version remains unchanged.

## What Does "Immutable" Mean?
In computer science, an immutable value is a value whose contents **cannot be modified after creation**. In JavaScript, all primitives (including strings, numbers, booleans, and symbols) are immutable.

---

## Variable Reassignment vs. Value Mutation
A common point of confusion for beginners is:
```javascript
let str = "hello";
str = "world"; // "I changed the string, so it must be mutable!"
```
**This is not value mutation.** You did not change the string value `"hello"`. You changed **which string the variable points to**.

### The Mental Model: Moving Houses
Imagine your friend lives at `123 Main Street` ("hello") and then moves to `456 Oak Street` ("world"). 
* Did the physical house at `123 Main Street` change? **No.**
* Your friend simply changed addresses. 
Similarly, the value `"hello"` still exists in memory until the garbage collector reclaims it. The variable `str` was simply rebound to point to a new value:

```
Before:
str ─────────► "hello"

After:
str ─────────► "world" (New primitive value allocated)
               "hello" (Old primitive value left orphan in memory)
```

---

## Character Level Mutation Failure
Because strings are immutable, attempting to change a specific index has no effect:
```javascript
let str = "hello";
str[0] = "H"; 

console.log(str); // "hello" (No change!)
```
In loose mode, the assignment fails silently. In strict mode (`"use strict";`), it throws a `TypeError: Cannot assign to read only property '0' of string`.

---

## Why Do All String Methods Return New Strings?
Since the original string cannot be changed, methods like `slice()`, `replace()`, `trim()`, and `toUpperCase()` always return a **brand-new string value**.

If string methods mutated values in-place, shared memory optimizations would lead to catastrophic side-effects:

```javascript
let a = "cat";
let b = a; // Shares memory pointer with a

a.toUpperCase(); // Suppose this mutated "cat" to "CAT" in memory
console.log(b);  // "CAT" (Wait! Variable b unexpectedly changed!)
```
By forcing methods to return new instances, JavaScript ensures program predictability.

```javascript
let a = "cat";
let b = a;
let c = a.toUpperCase(); // Creates a new string "CAT"

console.log(a); // "cat" (Safe!)
console.log(b); // "cat" (Safe!)
console.log(c); // "CAT" (Expected!)
```

---

## Why Did JavaScript Choose String Immutability?
1. **Safety**: Strings are often used to store critical information (URLs, passwords, file paths). If they were mutable, a rogue helper function could silently alter this data.
2. **Memory Optimizations**: Enables the engine to share memory addresses between variables and perform **[[js-string-memory-storage|String Interning]]**.
3. **Faster Comparisons**: Since a string's content is guaranteed never to change, engines can compare references/hashes instead of scanning large strings character-by-character.
4. **Concurrency**: Web Workers can safely share string values across execution contexts without race conditions or locks.

---

## Strings (Immutable) vs. Arrays (Mutable)

| Action | Strings | Arrays |
| :--- | :---: | :---: |
| **Index Modification (`x[0] = "a"`)** | Fails silently (or throws) | Modifies element in-place |
| **Methods modify original value?** | No (returns new string) | Yes (e.g., `push()`, `splice()`) |
| **Pass-by-value / Pass-by-sharing** | Reassignment inside function does not affect outer variable | Modifying array properties inside function affects outer array |

---

## Canonical Code Example

This script highlights the immutability of string parameters, indexing operations, and compares the behavior to arrays:

```javascript
// --- 1. Silent Failure of String Index Write ---
const filename = "config.json";
filename[0] = "C"; 

console.log("Write to string index:", filename); // "config.json"


// --- 2. String Method Non-Mutation ---
const user = "  luffy  ";
const trimmedUser = user.trim(); // Returns new string

console.log("Original value:", `|${user}|`); // "|  luffy  |" (spaces remain!)
console.log("Returned value:", `|${trimmedUser}|`); // "|luffy|"


// --- 3. Pass-By-Sharing Parameter Mutability ---
function convertToUppercase(text) {
  text = text.toUpperCase(); // Reassigns local parameter reference
  console.log("Inside function:", text); // "HELLO"
}

const greeting = "hello";
convertToUppercase(greeting);
console.log("Outside function:", greeting); // "hello" (Original remains untouched)


// --- 4. Contrast with Mutable Array Pass-By-Sharing ---
function replaceFirstElement(items) {
  items[0] = "Apples"; // Mutates the underlying reference object!
}

const shoppingList = ["Oranges", "Bananas"];
replaceFirstElement(shoppingList);
console.log("Shopping List after function call:", shoppingList); 
// ["Apples", "Bananas"] (Original mutated!)
```

---

## Related
* [[js-const-mutabilty]] - Const binds the variable box, whereas immutability locks the value itself.
* [[js-primitive-vs-reference-types]] - Comparing pass-by-value for primitives and pass-by-sharing for references.
* [[MOC - JS Objects & Structures]] - Hub comparing object mutability to primitive immutability.
