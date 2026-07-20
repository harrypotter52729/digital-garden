---
id: 202607201933
title: "Regex Matching: match"
aliases:
  - string-match
  - match
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
  - topic/regex
date_created: 2026-07-20
mastery_level: 1
---

# Regex Matching: match

> **TL;DR:** `match()` is like a **custom metal detector** (it uses regular expressions to scan a string for patterns and returns an array with match details, or `null` if nothing is found).

## Purpose & Behavior
The `match()` method retrieves the result of matching a string against a regular expression.

```javascript
string.match(regexp)
```

* **`regexp`**: A regular expression object. If a non-RegExp object is passed, it is implicitly converted to a RegExp using `new RegExp(val)`.

---

## Technical Details & Mechanics

### 1. Global vs. Non-Global Matching Mode
The return value of `match()` changes drastically depending on the presence of the global (`g`) flag:

* **Without the `g` flag:** It only retrieves the **first match** and returns a detailed array containing:
  * Index `0`: The full matched substring.
  * Index `1` to `n`: Captured groups (if any).
  * `index`: The zero-based index where the match started.
  * `input`: The original source string.
  * `groups`: An object of named capturing groups, or `undefined`.
* **With the `g` flag:** It retrieves **all matches** in the string and returns a simple array of matching substrings. **It excludes all additional properties** (no `index`, `input`, or `groups` properties are attached).

### 2. The `null` Return Trap
If no matches are found, `match()` returns **`null`**, rather than an empty array `[]`. This is a frequent cause of production crashes when developers try to iterate or destructure results directly:

```javascript
// Throws TypeError: Cannot read properties of null (reading '0')
const matchIndex = "hello".match(/xyz/)[0]; 

// Correct Approach:
const result = "hello".match(/xyz/);
const matchVal = result ? result[0] : "";
```

---

## Comparison: `match()` vs. `includes()`
* Use [[js-string-includes|includes()]] if you only need a simple **yes/no** check for a literal substring. It is much faster because it does not build match objects.
* Use `match()` when you need to **extract the matching content** or search for complex regular expression patterns (like finding digits or parsing emails).

---

## Canonical Code Example

Here is a copy-pasteable script demonstrating non-global and global extraction, null checks, and email pattern matching:

```javascript
// --- 1. Non-Global Match (Detailed Array) ---
const phrase = "Java Java Python";
const singleMatch = phrase.match(/Java/);

console.log("Single match array details:");
console.log("- Matched text:", singleMatch[0]);     // "Java"
console.log("- Started at index:", singleMatch.index); // 0
console.log("- Original input:", singleMatch.input);  // "Java Java Python"


// --- 2. Global Match (Simple Array) ---
const globalMatch = phrase.match(/Java/g);
console.log("Global matches array:", globalMatch); // ["Java", "Java"]
console.log("Global match index (should be undefined):", globalMatch.index); // undefined


// --- 3. The Null Check Trap ---
const text = "Contact admin@test.com for details.";
const emailRegex = /\S+@\S+\.\S+/;

// Safe matching check
const emailMatch = text.match(emailRegex);
if (emailMatch) {
  console.log("Found email address:", emailMatch[0]); // "admin@test.com"
}

const noMatch = text.match(/no-such-pattern/);
console.log("No match return value:", noMatch); // null
// console.log(noMatch[0]); // CRASHES!


// --- 4. RegExp Flags Combination ---
const mixedCase = "Java JAVA java";
const caseInsensitiveMatch = mixedCase.match(/java/gi);
console.log("Case insensitive global match:", caseInsensitiveMatch); // ["Java", "JAVA", "java"]
```

---

## Related
* [[js-string-matchall]] - Returning an iterator with detailed match properties for every single match.
* [[js-string-includes]] - Simple boolean checks.
* [[js-string-search]] - Finding the index position of a regex match.
* [[MOC - JS Built-in Objects & Utilities]] - Core MOC for built-in JavaScript operations.
