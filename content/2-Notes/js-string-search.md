---
id: 202607201935
title: "Regex Searching: search"
aliases:
  - string-search
  - search
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
  - topic/regex
date_created: 2026-07-20
mastery_level: 1
---

# Regex Searching: search

> **TL;DR:** `search()` is like checking if a **specific badge pattern is present in a group** (it returns the **starting index** of the first match that fits a regex pattern, or `-1` if nothing matches).

## Purpose & Behavior
The `search()` method executes a search for a match between a regular expression and this String object.

```javascript
string.search(regexp)
```

* **`regexp`**: A regular expression object. If a non-RegExp is passed, it is implicitly converted to a RegExp using `new RegExp(val)`.

---

## Technical Details & Mechanics

### 1. Simple Return: Index or -1
Unlike [[js-string-match|match()]] which returns a detailed array of match contents and capture groups, `search()` only returns:
* A non-negative integer indicating the starting index of the **first** match.
* **`-1`** if no match is found.

### 2. The Global Flag (`g`) is Ignored
Because `search()` only cares about the *first* match position, **the global flag (`g`) has no effect**.
* `"cat cat".search(/cat/g)` checks the string, finds the first match at index `0`, and immediately returns `0` (ignoring subsequent matches).

### 3. Comparison with `indexOf()` and `match()`

| Method | Argument | Returns | Use Case |
| :--- | :--- | :--- | :--- |
| **`indexOf()`** | Literal String | Index or `-1` | Best for simple, fast literal string indexing. |
| **`search()`** | RegExp | Index or `-1` | Best for locating the position of a **pattern** match. |
| **`match()`** | RegExp | Array or `null` | Best when you need the **matched text** or capture groups. |

---

## Edge Cases & Quirks
* **Literal Strings:** If you pass a literal string like `"Java"`, `search()` converts it to `/Java/` internally.
* **Redundancy:** Do not use `search()` for simple substring checks. E.g., `if (str.search("abc") !== -1)` is slower and less clear than `if (str.includes("abc"))`. Use `search()` only when regex pattern validation is required.

---

## Canonical Code Example

Here is a script demonstrating digit searches, special character validation, and key comparisons:

```javascript
// --- 1. Real World: Locate First Digit ---
const log = "ERROR 500: Database connection failed.";
// Search for first number digit (\d)
const firstDigitIndex = log.search(/\d/);

console.log("First digit index position:", firstDigitIndex); // 6 (starts at "5")


// --- 2. Real World: Locate First Special Character ---
const password = "mySecurePassword123!";
// Match any character that is NOT a letter or digit
const specialCharIndex = password.search(/[^a-zA-Z0-9]/);

console.log("First special char index:", specialCharIndex); // 20 (starts at "!")


// --- 3. Ignored Global Flag Demonstration ---
const animals = "cat dog cat bird";
// Even with /g flag, search returns only the first index
console.log("Global regex search index:", animals.search(/cat/g)); // 0


// --- 4. No Match Case ---
const text = "rhythm";
// Search for a vowel character
const vowelIndex = text.search(/[aeiou]/);
console.log("First vowel index:", vowelIndex); // -1 (no match)


// --- 5. search() vs. indexOf() ---
const searchString = "Order #12345";
try {
  // indexOf fails to parse RegExp
  searchString.indexOf(/\d+/);
} catch (e) {
  console.log("indexOf fails on regex patterns");
}

console.log("search() parses regex correctly:", searchString.search(/\d+/)); // 7
```

---

## Related
* [[js-string-indexof]] - Finding the index position of literal strings.
* [[js-string-match]] - Extracting matched substrings or capture groups.
* [[js-string-includes]] - Simple substring check yielding a boolean.
* [[MOC - JS Built-in Objects & Utilities]] - Central MOC index for standard utility methods.
