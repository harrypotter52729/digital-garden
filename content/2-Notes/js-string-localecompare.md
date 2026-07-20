---
id: 202607201936
title: "String Comparison: localeCompare"
aliases:
  - string-localecompare
  - localecompare
  - string-sorting
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
  - topic/type-conversion
date_created: 2026-07-20
mastery_level: 1
---

# String Comparison: localeCompare

> **TL;DR:** `localeCompare()` is like asking a **multilingual dictionary translator to sort folders** (it understands that different languages have distinct alphabetical rules, ensuring that letters, accents, and symbols sort naturally according to human language, not raw machine code).

## Purpose & Behavior
The `localeCompare()` method returns a number indicating whether a reference string comes before, after, or is the same as the given string in sort order.

```javascript
referenceStr.localeCompare(compareString, locales, options)
```

* **`compareString`**: The string against which `referenceStr` is compared.
* **`locales` & `options` (Optional)**: Customize collation behavior (e.g. ignoring case, choosing language-specific sorting).

### Return Value Guarantees
The ECMAScript specification **only guarantees the sign of the return value**, not the exact integer.
* **Negative number ($< 0$):** `referenceStr` comes **before** `compareString`.
* **Positive number ($> 0$):** `referenceStr` comes **after** `compareString`.
* **Zero ($= 0$):** The strings are considered **equal**.

> [!WARNING]
> Do not write conditions checking for exact returns like `-1` or `1`:
> ```javascript
> if (a.localeCompare(b) === -1) { ... } // Bad! (Browser engines may return -2, -10, etc.)
> if (a.localeCompare(b) < 0) { ... }    // Correct!
> ```

---

## Why standard operators (`<`, `>`) fail for human sorting
Standard comparison operators (e.g., `"apple" < "banana"`) compare strings character-by-character using **Unicode code point values**. This leads to unexpected results for humans:

1. **Case Sensitivity:** Uppercase letters have smaller Unicode values than lowercase letters (`"A"` is `65`, `"a"` is `97`).
   * `"Zoo" < "apple"` returns `true` because `"Z"` (`90`) is smaller than `"a"` (`97`). Alphabetically, "apple" should come first.
2. **International Accents:**
   * In Swedish, `"Å"` is sorted near the end of the alphabet. In German, `"ä"` is sorted near `"a"`.
   * Standard Unicode comparison does not respect these rules.

`localeCompare()` solves this by loading locale-specific collation algorithms.

---

## Sensitivity Levels (Case-Insensitive Sorting)
You can customize comparison sensitivity using the `options` parameter:
* **`"base"`:** Only strings that differ in base letters are considered unequal (ignores case and accents, e.g. `a` == `á` == `A`).
* **`"accent"`:** Differences in base letters and accents are significant, but case is ignored (e.g. `a` == `A` but `a` $\neq$ `á`).
* **`"case"`:** Differences in base letters and case are significant, but accents are ignored (e.g. `a` == `á` but `a` $\neq$ `A`).
* **`"variant"` (Default):** Base letters, accents, and case are all significant (e.g. `a` $\neq$ `A` $\neq$ `á`).

---

## Canonical Code Example

Here is a copy-pasteable script demonstrating array sorting, case-insensitive comparison, and language-specific rules:

```javascript
// --- 1. Basic Sign Comparison ---
console.log("apple vs banana:", "apple".localeCompare("banana")); // Returns negative (< 0)
console.log("banana vs apple:", "banana".localeCompare("apple")); // Returns positive (> 0)
console.log("equal comparison:", "apple".localeCompare("apple"));   // Returns 0


// --- 2. Correct Array Sorting vs Unicode Default ---
const unsorted = ["banana", "Apple", "cat"];

// Bad: default sort converts to string and compares raw Unicode values
const badSort = [...unsorted].sort();
console.log("Default Unicode Sort:", badSort); // ["Apple", "banana", "cat"] ("A" comes before "b")

// Good: localeCompare sorts alphabetically
const goodSort = [...unsorted].sort((a, b) => a.localeCompare(b));
console.log("Human Alphabetical Sort:", goodSort); // ["Apple", "banana", "cat"] or ["apple" ... ] sorted naturally


// --- 3. Case-Insensitive Comparison ---
const first = "Apple";
const second = "apple";

// Base sensitivity ignores case differences
const isEqual = first.localeCompare(second, undefined, { sensitivity: "base" }) === 0;
console.log(`Are '${first}' and '${second}' equal under base sensitivity:`, isEqual); // true


// --- 4. International Accented Rules ---
const germanWord1 = "ä";
const germanWord2 = "z";

// German locale orders umlaut 'ä' near base 'a' (comes before 'z')
console.log("German locale sort (ä vs z):", germanWord1.localeCompare(germanWord2, "de")); // Negative (< 0)
```

---

## Related
* [[js-string-case-conversion]] - Normalizing strings manually for simple comparison.
* [[js-string-normalize]] - Standardizing Unicode code point compositions.
* [[js-strict-vs-loose-equality]] - Equality rules for string comparisons.
* [[MOC - JS Built-in Objects & Utilities]] - Central hub for built-in helpers.
* [[MOC - JS Type Conversion & Equality]] - Data matching rules in JavaScript.
