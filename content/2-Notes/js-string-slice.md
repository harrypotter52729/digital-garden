---
id: 202607201920
title: "String Extraction: slice"
aliases:
  - string-slice
  - slice
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
date_created: 2026-07-20
mastery_level: 1
---

# String Extraction: slice

> **TL;DR:** `slice()` is like using a **sharp knife to cut a section out of a ribbon** (you specify the start and end markings, and it cleanly cuts that chunk out, leaving the original ribbon completely untouched).

## Purpose & Behavior
The `slice()` method extracts a section of a string and returns it as a new string, without modifying the original string.

```javascript
string.slice(start, end)
```

* **`start`**: The zero-based index at which to begin extraction. If negative, it is treated as `str.length + start`.
* **`end` (Optional)**: The zero-based index *before* which to end extraction. The character at this index is **excluded**. If omitted, it slices to the end of the string. If negative, it is treated as `str.length + end`.

---

## Technical Details & Mechanics

### 1. Why Is the End Index Excluded?
Excluding the end index is a standard design decision in programming languages. It ensures mathematical predictability:
* **The Length Formula:** The length of the returned string is always exactly $\text{end} - \text{start}$.
  * `"JavaScript".slice(0, 4)` returns a string of length $4 - 0 = 4$ (`"Java"`).
  * `"JavaScript".slice(4, 10)` returns a string of length $10 - 4 = 6$ (`"Script"`).

### 2. Negative Index Translation
Negative indices allow you to target characters relative to the end of the string. The engine translates negative values into positive offsets before slicing:
$$\text{Calculated Index} = \text{Length} + \text{Negative Index}$$

* For `"JavaScript"` (length `10`), calling `slice(-3)` is calculated as `10 + (-3) = 7`. The engine executes `slice(7)`, yielding `"ipt"`.
* If a negative index offsets beyond the start of the string (e.g. `slice(-100)` on a length `10` string), the engine **clamps** the value to `0`.

### 3. Out-of-Bounds & Swap Behavior
* **Start > End:** If the start index resolves to a position greater than the end index, `slice()` does **not** swap them. It returns an empty string `""` because it is impossible to traverse backward.
* **Large Start:** If `start` $\ge$ `str.length`, it returns `""`.
* **Large End:** If `end` exceeds the string length, it extracts to the end of the string.

---

## Performance & Memory
* **Time Complexity:** $O(k)$ where $k$ is the number of characters copied (not the length of the source string).
* **Memory Allocation:** Strings are [[js-string-immutability|immutable]]. Slicing allocates memory for a brand new primitive string containing the copied characters.

---

## Canonical Code Example

Here is a script demonstrating filename extraction, negative slicing, and boundary conditions:

```javascript
// --- 1. Extracting File Extensions ---
const file = "archive.tar.gz";

// Bad way (fixed offset, fails if extension length differs)
console.log("Fixed negative slice:", file.slice(-2)); // "gz" (fails for ".html", etc.)

// Good way (combine lastIndexOf and slice)
const lastDotIndex = file.lastIndexOf(".");
const extension = file.slice(lastDotIndex + 1);
console.log("Dynamic extension extraction:", extension); // "gz"


// --- 2. String Boundary Model ---
const phrase = "JavaScript";
// Slicing "Scr"
// J a v a S c r i p t
// 0 1 2 3 4 5 6 7 8 9 10 (boundaries)
//        |-----|
//        4     7
console.log("Boundary slice (4, 7):", phrase.slice(4, 7)); // "Scr"


// --- 3. Negative Index Offsets ---
// length = 10. Start: 10 - 6 = 4. End: 10 - 3 = 7. Equivalent to slice(4, 7)
console.log("Negative start & end:", phrase.slice(-6, -3)); // "Scr"


// --- 4. Boundary & Clamping Quirks ---
console.log("Start > End:", phrase.slice(5, 2)); // "" (no swapping, returns empty)
console.log("Exceeding negative clamp:", phrase.slice(-100)); // "JavaScript" (clamped to 0)
console.log("Exceeding positive index:", phrase.slice(100));  // ""
```

---

## Related
* [[js-string-substring]] - Extraction method that swaps parameters and clamps negatives to 0.
* [[js-string-split]] - Dividing a string into an array of substrings.
* [[js-string-immutability]] - Explaining why original strings remain unchanged.
* [[MOC - JS Data Types & Memory]] - Memory allocation details for primitive types.
* [[MOC - JS Built-in Objects & Utilities]] - Central directory for object methods.
