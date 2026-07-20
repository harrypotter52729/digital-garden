---
id: 202607201929
title: "String Repetition: repeat"
aliases:
  - string-repeat
  - repeat
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
date_created: 2026-07-20
mastery_level: 1
---

# String Repetition: repeat

> **TL;DR:** `repeat()` is like using a **rubber stamp to print the same word on a page multiple times** (it creates a new string containing the original text repeated a specified number of times).

## Purpose & Behavior
The `repeat()` method constructs and returns a new string containing the specified number of copies of the string on which it was called, concatenated together.

```javascript
string.repeat(count)
```

* **`count`**: An integer indicating the number of times to repeat the string.

---

## Technical Details & Mechanics

### 1. Fractional Value Truncation
The specification converts the `count` parameter to an integer by **truncating the fractional part** (effectively rounding toward zero). It does not perform standard rounding.
* `"Hi".repeat(2.8)` is truncated to `2`, returning `"HiHi"`.
* `"Hi".repeat(2.1)` is truncated to `2`, returning `"HiHi"`.

### 2. The RangeError Traps
Calling `repeat()` with invalid count values will throw a **`RangeError`**:
* **Negative Numbers:** You cannot repeat a string a negative number of times.
  ```javascript
  "Hi".repeat(-1); // RangeError: Invalid count value
  ```
* **Infinity:** Attempting to repeat a string infinite times would exhaust memory.
  ```javascript
  "Hi".repeat(Infinity); // RangeError: Invalid count value
  ```
* **Engine Limits:** Passing a huge repeat count (e.g. `repeat(1000000000)`) can also throw a `RangeError` because the resulting string would exceed the maximum string length allowed by the engine (e.g., V8 limit is typically 512MB to 1GB).

### 3. Coercion & NaN Behavior
If the count argument is not a number, JavaScript coerces it:
* **NaN:** `NaN` is coerced to `0`. `"Hi".repeat(NaN)` returns `""`.
* **Boolean values:** `true` is coerced to `1` (repeats once), and `false` is coerced to `0` (returns `""`).
* **Numeric Strings:** `"Hi".repeat("3")` converts `"3"` to `3`, returning `"HiHiHi"`.
* **Invalid Strings:** `"Hi".repeat("abc")` converts `"abc"` to `NaN`, which is treated as `0`, returning `""`.

---

## Real-World Applications
`repeat()` is frequently used for text-based formatting, console layouts, or masking:

```javascript
// Printing separator lines in CLI
console.log("-".repeat(50)); // "--------------------------------------------------"

// Creating a terminal progress bar
const progress = 7;
console.log("█".repeat(progress)); // "███████"

// Password/Credit card masking
const password = "mySecretPassword";
console.log("*".repeat(password.length)); // "****************"
```

---

## Canonical Code Example

Here is a copy-pasteable script highlighting coercion anomalies and exception traps:

```javascript
// --- 1. Basic Repetition ---
console.log("Repeat 3 times:", "abc".repeat(3)); // "abcabcabc"
console.log("Repeat 0 times:", "abc".repeat(0)); // "" (returns empty string)


// --- 2. Truncation of Fractional Counts ---
// 2.9 is truncated to 2
const fractionalResult = "A".repeat(2.9);
console.log("Fractional repeat (2.9):", fractionalResult); // "AA"


// --- 3. Coercion Quirks ---
console.log("Repeat with true:", "Hi".repeat(true));   // "Hi" (true coerced to 1)
console.log("Repeat with false:", "Hi".repeat(false)); // ""   (false coerced to 0)
console.log("Repeat with NaN:", "Hi".repeat(NaN));     // ""   (NaN coerced to 0)
console.log("Repeat with 'abc':", "Hi".repeat("abc")); // ""   ("abc" -> NaN -> 0)
console.log("Repeat with '3':", "Hi".repeat("3"));     // "HiHiHi"


// --- 4. Exception Traps ---
function safeRepeat(str, count) {
  try {
    return str.repeat(count);
  } catch (error) {
    if (error instanceof RangeError) {
      console.log(`RangeError caught for count '${count}':`, error.message);
      return "";
    }
  }
}

safeRepeat("Hi", -5);       // Caught RangeError
safeRepeat("Hi", Infinity); // Caught RangeError
```

---

## Related
* [[js-string-padstart-padend]] - Padding strings to a fixed width rather than copying arbitrarily.
* [[js-string-immutability]] - String repetition constructs a brand new string value.
* [[MOC - JS Built-in Objects & Utilities]] - Hub for standard JavaScript utility methods.
