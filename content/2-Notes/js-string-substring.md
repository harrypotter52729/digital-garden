---
id: 202607201921
title: "String Extraction: substring"
aliases:
  - string-substring
  - substring
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
date_created: 2026-07-20
mastery_level: 1
---

# String Extraction: substring

> **TL;DR:** `substring()` is like a **helpful assistant who tries to fix your mistakes** (if you pass negative values, it treats them as `0`, and if you accidentally swap the start and end indices, it silently swaps them back to extract the expected range).

## Purpose & Behavior
The `substring()` method extracts a section of a string and returns it as a new string, without modifying the original string. 

```javascript
string.substring(start, end)
```

* **`start`**: The zero-based index at which to begin extraction.
* **`end` (Optional)**: The zero-based index *before* which to end extraction. The character at this index is **excluded**.

---

## Technical Details & Mechanics

### 1. Index Swapping
If `start` is greater than `end`, `substring()` automatically **swaps the two arguments** so that traversal remains left-to-right.
* `str.substring(7, 2)` is silently converted to `str.substring(2, 7)`.
* This yields `"vaScr"` for `"JavaScript"`.
* *(Note: In contrast, [[js-string-slice|slice()]] does not swap parameters and returns an empty string `""` in this scenario).*

### 2. Negative Value Clamping
`substring()` **does not support negative indexing**.
* If `start` or `end` is less than `0` (or is `NaN`), it is coerced to `0`.
* `str.substring(-3)` is treated as `str.substring(0)`, returning the entire string.
* `str.substring(2, -1)` treats the negative index as `0`, becoming `str.substring(2, 0)`. The engine then detects `2 > 0`, swaps the arguments to `str.substring(0, 2)`, and returns the first two characters.

### 3. Out-of-Range Clamping
If either argument is greater than the string's length, it is clamped to `string.length`.
* `"JavaScript".substring(100, 2)` becomes `"JavaScript".substring(10, 2)`, which swaps to `substring(2, 10)`, returning `"Script"`.

---

## Why Modern Developers Prefer `slice()`
Historically, `substring()` was introduced in early JavaScript (ES1) to make string manipulation forgiving for beginners. However, modern developers prefer `slice()` because:
1. **Predictability:** Silently swapping arguments or converting negatives to `0` can hide bugs in logic.
2. **Negative Offsets:** Slicing from the end of a string (e.g. `slice(-4)`) is highly useful and cleaner than writing `str.substring(str.length - 4)`.
3. **API Consistency:** Arrays support `slice()`, but do not have a `substring()` method. Using `slice()` keeps string and array operations uniform.

---

## Canonical Code Example

Here is a copy-pasteable script highlighting the differences between parameter configurations:

```javascript
const text = "JavaScript";

// --- 1. Basic Extraction ---
console.log("Basic slice-like range:", text.substring(0, 4)); // "Java"


// --- 2. Demonstrating Index Swapping ---
// start (7) > end (2) -> swapped to substring(2, 7)
const swapped = text.substring(7, 2);
console.log("Swapped arguments (7, 2):", swapped); // "vaScr"


// --- 3. Negative Index Handling ---
// Negative index (-4) coerced to 0 -> substring(0)
console.log("Single negative index (-4):", text.substring(-4)); // "JavaScript"

// Coerces to substring(2, 0) -> swaps to substring(0, 2)
console.log("Mixed negative index (2, -1):", text.substring(2, -1)); // "Ja"


// --- 4. Out-of-Bounds Clamping ---
// Coerces to substring(10, 2) -> swaps to substring(2, 10)
console.log("Clamped out-of-bounds (100, 2):", text.substring(100, 2)); // "vaScript"
```

---

## Related
* [[js-string-slice]] - String extraction supporting negative indexes without argument swapping.
* [[js-string-slice-vs-substring-vs-substr]] - A detailed comparison of the extraction methods.
* [[MOC - JS Built-in Objects & Utilities]] - Hub for built-in operations.
