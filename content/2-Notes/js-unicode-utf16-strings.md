---
id: 202607112245
title: Unicode, UTF-16, and Surrogate Pairs in JavaScript
aliases:
  - string-unicode
  - surrogate-pairs
  - utf16-strings
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/unicode
date_created: 2026-07-11
mastery_level: 1
---

# Unicode, UTF-16, and Surrogate Pairs in JavaScript

> **TL;DR:** A character is like a **traveler** with a **Passport ID** (Unicode Code Point) representing their identity. Most travelers travel with **one suitcase** (one 16-bit Code Unit), but special travelers (like emojis or historical glyphs) require **two suitcases** (a Surrogate Pair of 16-bit Code Units) to make the journey.

## The Evolution of Encodings

### 1. ASCII (Old Standard)
Early computers used ASCII, which maps characters to numbers using 7 bits. It supports only 128 characters, which is enough for English (e.g., `A = 65`, `a = 97`) but fails to represent currencies (`€`), accents (`é`), non-Latin scripts (Chinese, Hindi), or emojis.

### 2. Unicode (Universal Standard)
Unicode solved this by creating a giant dictionary where **every character in every writing system gets a unique number** called a **Code Point**. 
* Code points are written in hexadecimal format prefixed by `U+` (e.g., `A` is `U+0041`, the rupee sign `₹` is `U+20B9`, and the smiley emoji `😊` is `U+1F60A`).

---

## How JavaScript Stores Strings: UTF-16
Computers cannot store code points directly; they must convert them into binary bytes. This mapping is called an encoding. 

JavaScript uses **UTF-16** internally. In UTF-16, memory is allocated in chunks of 16 bits, known as **Code Units**.

```
+------------------------+
|  16-bit UTF-16 Box     |  <-- Code Unit
+------------------------+
```

### The Basic Multilingual Plane (BMP) vs. Astral Planes
* **BMP (Code points $\le$ U+FFFF)**: Standard letters, numbers, and common symbols fit entirely inside a single 16-bit code unit.
* **Astral Planes (Code points $>$ U+FFFF)**: Emojis and rare historical characters are too large for 16 bits. UTF-16 splits these characters into **two consecutive 16-bit code units** known as a **Surrogate Pair** (consisting of a High Surrogate and a Low Surrogate).

---

## Why Emoji Break `.length`
In JavaScript, `.length` does **not** count human-visible characters or Unicode code points. **It counts the number of UTF-16 code units.**

```javascript
console.log("A".length); // 1 (1 code unit)
console.log("🚀".length); // 2 (1 code point, but 2 code units!)
console.log("AB🚀".length); // 4 (1 + 1 + 2)
```

```
Character "🚀" (Code Point U+1F680):
+--------------------+  +--------------------+
|  High Surrogate    |  |   Low Surrogate    |  => .length = 2
+--------------------+  +--------------------+
```

---

## The Indexing and character access Problem
Because bracket notation (`str[0]`) and `charAt(0)` read individual 16-bit code units, they fail when processing surrogate pairs:

```javascript
let smile = "😊";
console.log(smile[0]); // Returns High Surrogate (rendered as a broken glyph)
console.log(smile[1]); // Returns Low Surrogate
```

### The Solution: `codePointAt()` and `for...of`
To handle surrogate pairs correctly, use modern, Unicode-aware APIs:

1. **`charCodeAt()` vs `codePointAt()`**:
   * `charCodeAt(index)` returns the 16-bit numeric value at that index (only half the emoji).
   * `codePointAt(index)` understands surrogate pairs and retrieves the full 32-bit code point number.

2. **Index Loops vs `for...of` Loops**:
   * A standard `for (let i=0; i<str.length; i++)` loop iterates by **code units**, splitting emojis.
   * A `for...of` loop uses the string's built-in iterator, which is Unicode-aware and correctly iterates over **code points**.

---

## Grapheme Clusters & Normalization
Sometimes, even code points aren't enough. A single user-perceived character (a **Grapheme Cluster**) can be composed of multiple code points.

### Examples:
* **Zero Width Joiners (ZWJ)**: The family emoji 👨‍👩‍👧 is created by combining the Man, Woman, and Girl emojis with ZWJ characters (`\u200D`).
* **Combining Accents**: The character `é` can be represented in two ways:
  1. A single precomposed code point (`U+00E9`).
  2. A base character `e` (`U+0065`) plus a combining acute accent mark (`U+0301`).

Both look identical to the user, but they have different lengths and fail equality tests:

```javascript
let norm1 = "\u00E9"; // "é" (length 1)
let norm2 = "e\u0301"; // "é" (length 2)
console.log(norm1 === norm2); // false!
```

### The Solution: `String.prototype.normalize()`
To compare them safely, normalize them into a standard, precomposed form:
```javascript
console.log(norm1 === norm2.normalize()); // true
```

---

## Canonical Code Example

This code illustrates ASCII, Unicode code points, surrogate pairs, iterating over strings containing emojis, and normalization:

```javascript
// --- 1. Code Units vs Code Points ---
const rocket = "🚀"; // Code Point: U+1F680

console.log("Rocket .length:", rocket.length); // 2 (Code units)
console.log("charCodeAt(0) - High Surrogate:", rocket.charCodeAt(0)); // 55357
console.log("charCodeAt(1) - Low Surrogate:", rocket.charCodeAt(1));  // 56960
console.log("codePointAt(0) - Full Code Point:", rocket.codePointAt(0)); // 128640 (decimal of 1F680)


// --- 2. Iteration Comparison ---
const phrase = "A 🚀";

console.log("--- 2a. Code-Unit Loop (Broken) ---");
for (let i = 0; i < phrase.length; i++) {
  console.log(`Unit ${i}:`, phrase[i]);
}
// Outputs:
// Unit 0: A
// Unit 1:  
// Unit 2:  (Broken High Surrogate)
// Unit 3:  (Broken Low Surrogate)

console.log("--- 2b. Code-Point Loop (Correct) ---");
for (const char of phrase) {
  console.log("Point:", char);
}
// Outputs:
// Point: A
// Point:  
// Point: 🚀 (Iterated correctly!)


// --- 3. Normalization ---
const precomposed = "\u00E9"; // "é"
const decomposed = "e\u0301";  // "e" + "◌́"

console.log("Visual matches:", precomposed, "vs", decomposed); // é vs é
console.log("String Lengths:", precomposed.length, "vs", decomposed.length); // 1 vs 2
console.log("Equal strictly?:", precomposed === decomposed); // false

// Normalizing decomposed string to NFC (Normalized Form Canonical Composition)
const fixedDecomposed = decomposed.normalize("NFC");
console.log("After Normalization Length:", fixedDecomposed.length); // 1
console.log("Equal after normalization?:", precomposed === fixedDecomposed); // true
```

---

## Related
* [[js-string-charcodeat-vs-codepointat]] - Reading 16-bit code units vs full Unicode code points.
* [[js-string-normalize]] - Unicode composition and decomposition normalizations (NFC/NFD).
* [[js-for-of-iterator]] - Iterating safely over strings and code points.
* [[js-string-character-access]] - How indexing retrieves code units vs code points.
* [[MOC - JS Type Conversion & Equality]] - Equality concerns when comparing decomposed string representations.
