---
id: 202607201930
title: "String Padding: padStart and padEnd"
aliases:
  - string-padding
  - padstart
  - padend
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
date_created: 2026-07-20
mastery_level: 1
---

# String Padding: padStart and padEnd

> **TL;DR:** **Padding** is like **setting up column widths in a database report** (you specify a target width, and if your string is too short, the method automatically fills the empty space with leading or trailing characters to align it).

## Purpose & Behavior
These methods pad a string to a target length using a repeating padding string. 

* **`padStart()`**: Inserts padding characters at the **beginning** (left) of the string.
* **`padEnd()`**: Appends padding characters at the **end** (right) of the string.

```javascript
string.padStart(targetLength, padString)
string.padEnd(targetLength, padString)
```

* **`targetLength`**: The length of the resulting string once padded. If this value is less than or equal to the current string's length, the string is returned as-is.
* **`padString` (Optional)**: The string to pad with. Defaults to a single space (`" "`).

---

## Technical Details & Mechanics

### 1. The Repeating and Truncation Rule
If the padding required is larger than `padString.length`, the padding string is repeated. If the padding required does not divide evenly by `padString.length`, the final repetition is **truncated from the right**:

* **Example (Exact fit):** `"Hi".padStart(7, "12345")` needs `5` padding characters. `"12345"` fits exactly: `"12345Hi"`.
* **Example (Truncated pad):** `"Hi".padStart(6, "12345")` needs `4` characters. `"12345"` is truncated to `"1234"`, yielding `"1234Hi"`.
* **Example (Repeated pad):** `"Hi".padStart(8, "abc")` needs `6` characters. `"abc"` is repeated twice, yielding `"abcabcHi"`.

### 2. Advantage Over `repeat()`
Many developers write code like:
```javascript
"0".repeat(3 - String(num).length) + num; // Error prone manual calculation
```
Using `padStart()` is far superior because it **automatically computes the offset math** internally:
```javascript
String(num).padStart(3, "0"); // Self-calculating, clean, and robust
```

---

## Real-World Applications

### 1. Alignment & Time Formatting
```javascript
// Formatting invoice IDs
const invoice = "45";
invoice.padStart(6, "0"); // "000045"

// Time formatting (padding minutes/seconds)
const minutes = 5;
String(minutes).padStart(2, "0"); // "05"
```

### 2. Credit Card Masking
```javascript
const lastFour = "5678";
// Pad to length 16 with asterisks
lastFour.padStart(16, "*"); // "************5678"
```

### 3. Console Tables
Using `padEnd()` allows you to align text in tables printed to the terminal:
```javascript
console.log("ID".padEnd(10) + "NAME".padEnd(20));
console.log("1".padEnd(10) + "Monkey D Luffy".padEnd(20));
```

---

## Canonical Code Example

Here is a copy-pasteable script demonstrating padStart and padEnd operations:

```javascript
// --- 1. Basic padding with space & custom character ---
console.log("Pad start spaces:", "5".padStart(3));      // "  5" (defaults to space)
console.log("Pad start custom:", "5".padStart(3, "0")); // "005"
console.log("Pad end custom:", "5".padEnd(3, "0"));     // "500"


// --- 2. String Length > Target Length ---
// Slicing or truncating does NOT occur if the string is already longer
const longStr = "JavaScript";
console.log("Target length smaller:", longStr.padStart(5, "*")); // "JavaScript"


// --- 3. Padding String Truncation ---
const value = "Hi";
// Needs 4 characters of padding to reach length 6
// "12345" gets truncated from the right to "1234"
console.log("Truncated padding:", value.padStart(6, "12345")); // "1234Hi"


// --- 4. Real World: Credit Card Masking ---
function maskCardNumber(cardNumber) {
  const last4 = cardNumber.slice(-4);
  return last4.padStart(cardNumber.length, "*");
}

console.log("Masked Card:", maskCardNumber("1234567812345678")); // "************5678"
```

---

## Related
* [[js-string-repeat]] - Manual string duplication helper.
* [[js-string-slice]] - Extracting segments (e.g. getting the last 4 digits).
* [[js-string-character-access]] - Traditional character indexing.
* [[MOC - JS Built-in Objects & Utilities]] - Central MOC index for standard utility methods.
