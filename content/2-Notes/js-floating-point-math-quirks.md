---
id: 202606120836
title: Floating Point Math Quirks
aliases:
  - floating-point
  - js-math-quirks
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/math
date_created: 2026-06-12
mastery_level: 1
---

# Floating Point Math Quirks

> **TL;DR:** Floating-point math is like trying to measure distance using **stretchy rulers**. When you convert base-10 decimals like `0.1` and `0.2` into base-2 binary, they become infinite repeating decimals. The computer has to cut them off, causing tiny rounding errors that result in `0.1 + 0.2 !== 0.3`.

## The Mental Model
In base-10 math (the math humans use), some fractions cannot be cleanly written out as decimals. 
For example, `1 / 3` is `0.333333...` forever. If you round it to `0.33` and add three of them together, you get `0.99`, not `1`.

Computers run on binary (base-2 math). Fractions like `1 / 10` (which is `0.1`) cannot be cleanly represented in binary. They turn into repeating decimals: `0.000110011001100...` forever. 
Because the computer has to fit this number into a finite 64-bit space, it rounds off the tail. When it adds two rounded fractions together, the rounded error bubbles to the surface.

---

## 1. The IEEE 754 Standard
JavaScript stores all numbers (both integers and decimals) as **Double Precision 64-bit floats**. 

Because of this:
```javascript
console.log(0.1 + 0.2); // 0.30000000000000004
```
This is not a bug in JavaScript; it is standard behavior in almost all modern programming languages (Python, Java, C++, Ruby) that share the IEEE 754 spec.

---

## 2. How to Work Around the Quirks

### Method A: Scale to Integers (Best for Money)
Do not do math on decimals. Convert everything to integers (e.g., store money in cents rather than dollars), perform the math, and scale back down:
```javascript
const price1 = 0.1;
const price2 = 0.2;
const total = (price1 * 100 + price2 * 100) / 100; // 0.3 (Perfect!)
```

### Method B: Tolerance Checks (`Number.EPSILON`)
If you need to compare two decimals, check if the difference between them is smaller than `Number.EPSILON` (the smallest margin of error).
```javascript
const equal = Math.abs(0.1 + 0.2 - 0.3) < Number.EPSILON; // true
```

### Method C: String Formatting (`toFixed()`)
Truncate decimals to a specific decimal length.
```javascript
const totalStr = (0.1 + 0.2).toFixed(2); // "0.30" (Note: returns a string!)
```

---

## Canonical Code Example

Here is a script displaying float rounding errors and safe decimal comparison strategies:

```javascript
// --- 1. The Classic Quirk ---
const sum = 0.1 + 0.2;
console.log("0.1 + 0.2 =", sum); // 0.30000000000000004

// Directly comparing them fails:
if (sum === 0.3) {
  console.log("They are equal!");
} else {
  console.log("They are NOT equal!"); // This prints!
}


// --- 2. Solution: Safe Comparison using Number.EPSILON ---
function areDecimalsEqual(num1, num2) {
  // If the difference is smaller than the precision limit, treat them as equal
  return Math.abs(num1 - num2) < Number.EPSILON;
}

console.log("Safe equal check:", areDecimalsEqual(sum, 0.3)); // true


// --- 3. Solution: Formatting and Scaling ---
// Option 1: Truncating decimals
const formatted = parseFloat(sum.toFixed(12));
console.log("Formatted check:", formatted === 0.3); // true

// Option 2: Scaling (e.g. adding $0.10 and $0.20 as cents)
const centSum = (10 + 20) / 100; // 0.30
console.log("Scaled check:", centSum === 0.3); // true
```

---

## Related
* [[MOC - JS Data Types & Memory]] - Floating-point memory encoding.
* [[js-primitive-vs-reference-types]] - Primitives compared directly by value.
* [[js-object-freeze-vs-seal]] - Freezing mathematical config properties.
