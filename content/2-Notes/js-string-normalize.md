---
id: 202607201937
title: "Unicode Normalization: normalize"
aliases:
  - unicode-normalization
  - string-normalize
  - normalize
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
  - topic/unicode
date_created: 2026-07-20
mastery_level: 1
---

# Unicode Normalization: normalize

> **TL;DR:** `normalize()` is like a **spellcheck coordinator for accents and special symbols** (it ensures that strings that look identical to humans, but are constructed using different behind-the-scenes Unicode code units, get converted into a single unified format so they compare and sort correctly).

## The Problem: Visually Identical, Internally Different
In Unicode, some characters can be represented in multiple valid ways. For example, the accented character **`é`** can be stored in two ways:

1. **Precomposed (Single Code Point):** Stored as a single character `é` (`U+00E9`). 
   * **String length:** `1`.
2. **Decomposed (Sequence of Code Points):** Stored as a base letter `e` (`U+0065`) followed by a **Combining Acute Accent** `◌́` (`U+0301`).
   * **String length:** `2`.

To a human, they look identical. But JavaScript compares strings by their exact Unicode values, causing comparison checks to fail:
```javascript
const str1 = "é";  // Precomposed (length = 1)
const str2 = "é";  // Decomposed  (length = 2)

console.log(str1 === str2); // false!
```

---

## Purpose & Behavior
The `normalize()` method returns the Unicode Normalization Form of the string.

```javascript
string.normalize(form)
```

* **`form` (Optional)**: A string specifying the Unicode Normalization Form. Can be `"NFC"`, `"NFD"`, `"NFKC"`, or `"NFKD"`. Defaults to `"NFC"`.

---

## The Four Normalization Forms

### 1. NFC (Canonical Composition) — Default
Replaces decomposed sequences with precomposed equivalents where possible.
* Combines base letters and accents back into a single character.
* `"e"` + `◌́` $\rightarrow$ `"é"`.
* Highly recommended for general comparisons, database storage, and user authentication.

### 2. NFD (Canonical Decomposition)
Decomposes precomposed characters into their base letters and combining marks.
* Splits accents from their letters.
* `"é"` $\rightarrow$ `"e"` + `◌́`.
* Extremely useful for **accent-insensitive searching** (see example below).

### 3. NFKC (Compatibility Composition)
Decomposes compatibility characters and recomposes them canonically. It converts alternative typographical representations of symbols into standard characters.
* Example: Compatibility number `"①"` becomes standard number `"1"`.
* Useful for normalizing noisy user input.

### 4. NFKD (Compatibility Decomposition)
Decomposes compatibility characters without recomposing.

---

## Real-World Case: Accent-Insensitive Search
When building search features, users searching for `"resume"` expect to find `"résumé"`. The standard technique splits the accents (using NFD) and then strips them out using a Unicode range regex (`\u0300` to `\u036f` representing combining marks):

```javascript
function stripAccents(text) {
  return text
    .normalize("NFD")                    // Split accents from characters
    .replace(/[\u0300-\u036f]/g, "")     // Strip combining accents
    .toLowerCase();
}

console.log(stripAccents("Résumé")); // "resume"
```

---

## Canonical Code Example

Here is a copy-pasteable script demonstrating comparison failures, normalization, and compatibility mappings:

```javascript
// --- 1. Demonstrating Comparison Failure ---
const precomposed = "café"; // length = 4
const decomposed = "café";  // length = 5 (e + acute accent)

console.log("Direct compare (no normalization):", precomposed === decomposed); // false
console.log("Precomposed length:", precomposed.length); // 4
console.log("Decomposed length:", decomposed.length);   // 5


// --- 2. Normalizing to NFC (Canonical Composition) ---
const normPre = precomposed.normalize("NFC");
const normDec = decomposed.normalize("NFC");

console.log("Normalized compare (NFC):", normPre === normDec); // true
console.log("NFC length:", normPre.length);                   // 4


// --- 3. Compatibility normalization (NFKC) ---
const compatSymbol = "①"; // Circled digit one
console.log("NFC normalize on ①:", compatSymbol.normalize("NFC"));   // "①" (NFC ignores compatibility differences)
console.log("NFKC normalize on ①:", compatSymbol.normalize("NFKC")); // "1" (NFKC simplifies to standard character)


// --- 4. Real World: Accent Insensitive Validation ---
const documentTitle = "Café Paris";
const query = "cafe";

function searchMatch(sourceText, searchQuery) {
  const cleanSource = sourceText.normalize("NFD").replace(/[\u0300-\u036f]/g, "").toLowerCase();
  const cleanQuery = searchQuery.normalize("NFD").replace(/[\u0300-\u036f]/g, "").toLowerCase();
  return cleanSource.includes(cleanQuery);
}

console.log("Accent-free search match:", searchMatch(documentTitle, query)); // true
```

---

## Related
* [[js-unicode-utf16-strings]] - Deep dive into Unicode storage, code points, and units.
* [[js-string-charcodeat-vs-codepointat]] - Low-level code unit inspection.
* [[js-string-localecompare]] - Collation rules for sorting non-English alphabets.
* [[MOC - JS Data Types & Memory]] - Storage allocations for primitive types.
* [[MOC - JS Type Conversion & Equality]] - Loose vs strict equality comparison behaviors.
* [[MOC - JS Built-in Objects & Utilities]] - Central hub for utility methods.
