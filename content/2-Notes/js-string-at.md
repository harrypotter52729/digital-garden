---
id: 202607201931
title: "String Character Access: at"
aliases:
  - string-at
  - at
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
date_created: 2026-07-20
mastery_level: 1
---

# String Character Access: at

> **TL;DR:** `at()` is like a **smart mail retrieval box** (you can request the letter at any drawer number, including negative positions to count backward from the last drawer, returning `undefined` if you open a drawer that doesn't exist).

## Purpose & Behavior
The `at()` method takes an integer value and returns a new string consisting of the single UTF-16 code unit located at the specified offset.

```javascript
string.at(index)
```

* **`index`**: The zero-based index of the character to be returned. Supports negative numbers to count backward from the end of the string.

---

## Technical Details & Mechanics

### 1. Negative Indexing Support
Introduced in ECMAScript 2022 (ES2022), `at()` resolves a long-standing complaint in JavaScript: the inability to easily fetch the last character of a string.
* **Backward counting:** If `index` is negative, the engine computes:
  $$\text{Target Index} = \text{Length} + \text{Negative Index}$$
* `str.at(-1)` returns the last character, eliminating the verbose `str[str.length - 1]` syntax.

### 2. Out-of-Bounds Behavior
If the resolved index is out of bounds (either $< -\text{length}$ or $\ge \text{length}$), `at()` returns **`undefined`**.

### 3. Comparison with Bracket Notation and `charAt()`

| Feature | Bracket Notation (`str[index]`) | `charAt(index)` | `at(index)` |
| :--- | :---: | :---: | :---: |
| **Negative Indexes?** | ❌ No (returns `undefined`) | ❌ No (returns `""`) | **✅ Yes (counts backward)** |
| **Out-of-bounds return**| `undefined` | `""` (Empty string) | **`undefined`** |
| **Introduced in** | ES5 (2009) | ES1 (1997) | **ES2022 (2022)** |

* **Why didn't they change `charAt()`?** JavaScript prioritizes **backward compatibility**. Millions of existing websites rely on `charAt(-1)` returning `""` or `str[50]` returning `undefined`. Modifying `charAt` to return different values or support negative indices would break the web. Thus, a new method `at()` was introduced.

---

## The Emoji Limitation
Like bracket notation and `charAt()`, `at()` is **not Unicode-aware**. It reads a single **16-bit code unit** at a time, not full Unicode code points.

```javascript
const rocket = "🚀"; // Made of 2 code units: [High Surrogate, Low Surrogate]
console.log(rocket.at(0));  // Returns High Surrogate (broken character block)
console.log(rocket.at(-1)); // Returns Low Surrogate (broken character block)
```

To access complete surrogate pairs, you must use Unicode-aware APIs like `codePointAt()` or iterator loops.

---

## Canonical Code Example

Here is a copy-pasteable script demonstrating character access, negative indexing, and out-of-bounds comparison:

```javascript
const name = "JavaScript";

// --- 1. Fetching the Last Character ---
// Old verbose way
console.log("Old index lookup:", name[name.length - 1]); // "t"

// Modern clean way
console.log("Modern at(-1) lookup:", name.at(-1));        // "t"
console.log("Second to last character:", name.at(-2));     // "i"


// --- 2. Out-of-Bounds Differences ---
const outIndex = 50;

console.log("Bracket out-of-bounds:", name[outIndex]);       // undefined
console.log("charAt out-of-bounds:", `|${name.charAt(outIndex)}|`); // "||" (empty string)
console.log("at() out-of-bounds:", name.at(outIndex));       // undefined


// --- 3. The Logic Bug (Falsy Check Traps) ---
// Both undefined and empty string are falsy, but they behave differently in strict checks:
const bracketVal = name[outIndex];       // undefined
const charAtVal = name.charAt(outIndex); // ""
const atVal = name.at(outIndex);         // undefined

if (bracketVal === atVal) {
  console.log("Bracket and at() match on out-of-bounds"); // Prints
}

if (charAtVal !== atVal) {
  console.log("charAt and at() differ on out-of-bounds returns"); // Prints
}
```

---

## Related
* [[js-string-character-access]] - Original study on bracket notation vs charAt.
* [[js-string-charcodeat-vs-codepointat]] - Accessing low-level code units vs code points.
* [[js-unicode-utf16-strings]] - Exploring how surrogate pairs are stored in memory.
* [[MOC - JS Built-in Objects & Utilities]] - Central directory for built-in JavaScript operations.
