---
id: 202607112246
title: "String Character Access: Bracket Notation vs charAt"
aliases:
  - string-character-access
  - bracket-notation-vs-charat
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
date_created: 2026-07-11
mastery_level: 1
---

# String Character Access: Bracket Notation vs charAt

> **TL;DR:** Bracket notation `str[index]` acts like **accessing an object property** (if the property doesn't exist, it yields `undefined`). The `charAt(index)` method is a **built-in function** bound by the ECMAScript specification to return an empty string `""` for out-of-bounds searches.

## Low-Level Character Access
How does JavaScript retrieve a character from a string so quickly? Under the hood, strings are stored sequentially in memory. Because every UTF-16 code unit occupies exactly 16 bits of storage, the engine does not need to scan the string from the beginning to locate a character.

Instead, the memory address of the target character is calculated directly:
$$\text{Address} = \text{Base Address} + (\text{Index} \times 16\text{ bits})$$

### Time Complexity
Because retrieval uses direct address calculation:
* **Time Complexity:** $O(1)$ (Constant Time).
* This performance is identical whether the string has 5 characters, 5,000 characters, or 5 million characters. Accessing any index is a direct memory lookup.

---

## Bracket Notation vs. `charAt()`
There are two primary ways to access characters in JavaScript. While they look similar, they behave differently under edge cases (specifically, out-of-bounds checks).

### 1. Bracket Notation (`str[index]`)
Introduced in ECMAScript 5 (ES5), bracket notation mimics property lookup on an object. 

```javascript
let str = "cat";
console.log(str[0]);   // "c"
console.log(str[100]); // undefined
```
Since index `100` does not exist as a key on the string object, JavaScript returns `undefined`, just like accessing a non-existent property on a standard object (`obj["property"]`).

### 2. `charAt(index)`
`charAt()` is a standard string method that has existed since early versions of JavaScript.

```javascript
let str = "cat";
console.log(str.charAt(0));   // "c"
console.log(str.charAt(100)); // ""
```
The ECMAScript specification explicitly defines that if the index is out of bounds, `charAt()` must return an **empty string (`""`)**.

---

## Comparison Summary

| Feature | Bracket Notation (`str[index]`) | `charAt(index)` |
| :--- | :---: | :---: |
| **Out-of-bounds return** | `undefined` | `""` (Empty string) |
| **Performance** | $O(1)$ | $O(1)$ |
| **Under the Hood** | Property access emulation | Formal spec method execution |
| **Supported since** | ES5 (2009) | ES1 (1997) |

---

## The Emoji Catch
Neither method is Unicode-aware. Both read individual **16-bit code units** rather than Unicode code points.

```javascript
let smile = "😊"; // Made of 2 code units: [High Surrogate, Low Surrogate]

console.log(smile[0]);        // Returns High Surrogate (broken/unreadable character)
console.log(smile.charAt(0)); // Returns High Surrogate (broken/unreadable character)
```
To read complete emojis, index-based access is not recommended. You should use Unicode-aware methods such as `for...of` loops or `codePointAt()`.

---

## Canonical Code Example

The script below highlights indexing math behaviors, out-of-bounds differences, and how surrogate pair characters are retrieved:

```javascript
// --- 1. Basic Character Lookup ---
const word = "Developer";

console.log("Bracket lookup at 0:", word[0]);       // "D"
console.log("charAt lookup at 0:", word.charAt(0)); // "D"


// --- 2. Out of Bounds Comparisons ---
const shortStr = "cat";

const bracketResult = shortStr[50];
const charAtResult = shortStr.charAt(50);

console.log("Bracket Out-of-bounds:", bracketResult); // undefined
console.log("charAt Out-of-bounds:", `|${charAtResult}|`); // "||" (empty string)

// Interview Trap: Falsy checks
// Evaluating undefined vs empty string in logic
if (!bracketResult) {
  console.log("Bracket lookup is falsy (undefined)"); // Executes
}
if (charAtResult === "") {
  console.log("charAt lookup returns exact empty string"); // Executes
}


// --- 3. Lookup on Surrogate Pairs ---
const emoji = "🚀"; // Surrogate pair (length = 2)

const unit1 = emoji[0];
const unit2 = emoji.charAt(1);

console.log("Retrieved first half (High surrogate):", encodeURIComponent(unit1)); // %D8%BD (hex equivalent)
console.log("Retrieved second half (Low surrogate):", encodeURIComponent(unit2));  // %DE%80
// Note: Printing 'unit1' directly to the console yields a broken glyph block.
```

---

## Related
* [[js-unicode-utf16-strings]] - Deep dive into code units, points, and surrogate pair details.
* [[js-string-fundamentals]] - Fundamental traits of strings including length and zero-indexing.
* [[MOC - JS Built-in Objects & Utilities]] - Hub for built-in object methods.
