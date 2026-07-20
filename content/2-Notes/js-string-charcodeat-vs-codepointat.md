---
id: 202607201932
title: "Unicode Internals: charCodeAt vs codePointAt"
aliases:
  - charcodeat-vs-codepointat
  - surrogate-pairs
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
  - topic/unicode
date_created: 2026-07-20
mastery_level: 1
---

# Unicode Internals: charCodeAt vs codePointAt

> **TL;DR:** **charCodeAt()** is like reading **individual cargo boxes** (it inspects only a single 16-bit UTF-16 code unit, which splits emojis into two broken halves). **codePointAt()** is like reading the **shipping manifest** (it retrieves the full Unicode code point value, even if it spans multiple boxes).

## Under the Hood: UTF-16 & Surrogate Pairs
JavaScript strings are stored internally as a sequence of **16-bit code units** (using UTF-16 encoding). 
* **Basic Multilingual Plane (BMP):** Standard alphabetic characters (like `"A"`, `"z"`, or `"9"`) fit inside a single 16-bit code unit.
* **Supplementary Planes:** Emojis and ancient scripts require values larger than 16 bits. UTF-16 solves this by splitting the character into two 16-bit code units called a **Surrogate Pair** (a **High Surrogate** followed by a **Low Surrogate**).

Because `length` property counts **code units**, not human-visible characters:
```javascript
"🚀".length; // 2 (made of 2 code units)
```

---

## 1. `charCodeAt(index)`
Returns a number between `0` and `65535` representing the UTF-16 code unit at the specified index.
* If called on a surrogate pair, it only returns the value of one surrogate half, not the entire character.

---

## 2. `codePointAt(index)`
Returns a non-negative integer representing the complete Unicode code point value (which can exceed `65535`).
* If called on the index of a High Surrogate, it automatically reads the Low Surrogate next to it and returns the **entire Unicode code point**.
* If called directly on the index of a Low Surrogate (e.g. index 1 of a surrogate pair), it returns only that code unit value.

---

## Code Unit vs. Code Point

| Method | Returns | BMP Character (e.g. `"A"`) | Surrogate Pair (e.g. `"🚀"`) |
| :--- | :--- | :---: | :---: |
| **`charCodeAt(0)`** | UTF-16 code unit (decimal) | `65` | `55357` (High surrogate only) |
| **`codePointAt(0)`**| Full Unicode code point | `65` | `128640` (Complete rocket symbol) |

---

## Canonical Code Example

Here is a copy-pasteable script demonstrating code unit differences, surrogate pairs, and how emojis behave:

```javascript
// --- 1. BMP Character Lookup (Straightforward) ---
const letter = "A";
console.log("A - charCodeAt:", letter.charCodeAt(0));   // 65
console.log("A - codePointAt:", letter.codePointAt(0)); // 65


// --- 2. Surrogate Pair Lookup (Emoji) ---
const rocket = "🚀"; // Unicode: U+1F680 (Decimal: 128640)
// UTF-16 representation: High Surrogate (55357) + Low Surrogate (56960)

console.log("Length of rocket:", rocket.length); // 2

// charCodeAt gets only individual code units
const highSurrogateUnit = rocket.charCodeAt(0);
const lowSurrogateUnit = rocket.charCodeAt(1);
console.log("Rocket - High Surrogate unit:", highSurrogateUnit); // 55357
console.log("Rocket - Low Surrogate unit:", lowSurrogateUnit);   // 56960

// codePointAt gets the full Unicode representation
const fullCodePoint = rocket.codePointAt(0);
const trailingCodePoint = rocket.codePointAt(1);
console.log("Rocket - Full Code Point at 0:", fullCodePoint);      // 128640
console.log("Rocket - Trailing unit index 1:", trailingCodePoint); // 56960 (Low surrogate)


// --- 3. Interview Question: Hex conversion ---
// Unicode values are normally spoken in Hex (U+1F680)
console.log("Hex conversion of 128640:", fullCodePoint.toString(16).toUpperCase()); // "1F680"


// --- 4. The Broken charAt trap ---
// charAt reads only the code unit, yielding a broken symbol
console.log("Broken emoji output from charAt:", rocket.charAt(0)); // Prints a broken glyph block or ?
```

---

## Related
* [[js-unicode-utf16-strings]] - Deep dive into Unicode tables and UTF-16 encoding.
* [[js-string-character-access]] - Bracket notation vs charAt index boundaries.
* [[js-string-at]] - Accessing indices with negative values.
* [[js-string-normalize]] - Resolving visually identical Unicode characters.
* [[MOC - JS Data Types & Memory]] - Memory representations for JavaScript variables.
