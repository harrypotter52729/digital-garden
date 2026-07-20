---
id: 202607201922
title: "String Extraction: slice vs substring vs substr"
aliases:
  - string-extraction-comparison
  - slice-vs-substring
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
date_created: 2026-07-20
mastery_level: 1
---

# String Extraction: slice vs substring vs substr

> **TL;DR:** **Slice** is a **sharp knife** (performs exact cuts, supports negative offsets). **Substring** is a **forgiving assistant** (silently converts negative values to `0` and swaps parameters if they are in the wrong order). **Substr** is a **legacy ruler** (starts at a specific point and measures out a target **length** of characters).

---

## The Comparison Matrix

| Feature | `slice(start, end)` | `substring(start, end)` | `substr(start, length)` |
| :--- | :---: | :---: | :---: |
| **Second Parameter** | End index (exclusive) | End index (exclusive) | **Length** (number of characters) |
| **Negative Arguments** | Offsets from end (`length + index`) | Coerced to `0` | `start` offsets from end; `length` coerced to `0` |
| **If `start > end`** | Returns `""` (empty string) | **Swaps** parameters | N/A |
| **Specification Status** | Standard (Preferred) | Standard (Legacy) | **Annex B (Legacy / Deprecated)** |
| **Recommendation** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐ |

---

## 1. `slice(start, end)`
`slice()` is the modern standard for extracting substrings. It calculates negative values relative to the end of the string, making operations like "get the last 4 characters" extremely straightforward:
```javascript
"report.pdf".slice(-3); // "pdf"
```
If you pass indices in the wrong order (`start > end`), it returns `""` rather than guess-correcting your code.

---

## 2. `substring(start, end)`
`substring()` treats any negative number as `0` and swaps parameters if `start > end`. While useful for beginners, it can mask bugs:
```javascript
const name = "Luffy";
name.substring(-5); // "Luffy" (negative becomes 0)
name.substring(4, 1); // "uff" (swaps arguments to 1, 4)
```

---

## 3. `substr(start, length)`
Unlike the other two methods, the second parameter of `substr()` is a count of characters to extract (**length**), not an ending position.

```javascript
"JavaScript".substr(4, 6); // Starts at index 4 ('S') and extracts 6 characters -> "Script"
```

> [!CAUTION]
> `substr()` is a **legacy feature** defined in **Annex B** of the ECMAScript specification (which covers features kept only for web compatibility). Modern applications should avoid using `substr()` because it may not be supported consistently across all future runtimes or non-browser environments.

---

## Canonical Code Example

Here is a copy-pasteable script highlighting the differences under identical parameter inputs:

```javascript
const text = "JavaScript";

// --- Scenario A: Normal Range (2, 7) ---
// All three extract "vaScr" (substr takes 5 characters starting at 2)
console.log("slice(2, 7):", text.slice(2, 7));         // "vaScr"
console.log("substring(2, 7):", text.substring(2, 7)); // "vaScr"
console.log("substr(2, 5):", text.substr(2, 5));       // "vaScr"


// --- Scenario B: Swapped Parameters (7, 2) ---
console.log("slice(7, 2):", text.slice(7, 2));         // "" (invalid range, returns empty)
console.log("substring(7, 2):", text.substring(7, 2)); // "vaScr" (swapped to 2, 7)


// --- Scenario C: Negative Start (-4) ---
// slice(-4) -> slice(6) -> "ript"
console.log("slice(-4):", text.slice(-4));             // "ript"

// substring(-4) -> substring(0) -> "JavaScript"
console.log("substring(-4):", text.substring(-4));     // "JavaScript"

// substr(-4) -> starts at index 6 -> extracts to end -> "ript"
console.log("substr(-4):", text.substr(-4));           // "ript"


// --- Scenario D: Mixed Negative (2, -1) ---
// slice(2, 9) -> "vaScrip"
console.log("slice(2, -1):", text.slice(2, -1));       // "vaScrip"

// substring(2, 0) -> swaps to (0, 2) -> "Ja"
console.log("substring(2, -1):", text.substring(2, -1)); // "Ja"

// substr(2, 0) -> extracts 0 characters -> ""
console.log("substr(2, -1):", text.substr(2, -1));     // ""
```

---

## Related
* [[js-string-slice]] - Dedicated note on slice mechanics.
* [[js-string-substring]] - Dedicated note on substring index rules.
* [[MOC - JS Built-in Objects & Utilities]] - Navigation hub for standard JS APIs.
