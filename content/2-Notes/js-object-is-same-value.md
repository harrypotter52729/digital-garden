---
id: 202607280839
title: Object.is() & The SameValue Equality Algorithm
aliases:
  - Object.is
  - SameValue algorithm
  - SameValueZero algorithm
  - NaN equality
  - signed zero equality
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/comparison
  - topic/equality
date_created: 2026-07-28
mastery_level: 1
---

# `Object.is()` & The SameValue Equality Algorithm

> **TL;DR:** Strict equality `===` has two infamous IEEE-754 mathematical quirks: it claims `NaN === NaN` is `false`, but claims `-0 === +0` is `true`. `Object.is()` uses the **SameValue Algorithm**—a precision scientific microscope that treats `NaN` as strictly equal to `NaN`, and distinguishes negative zero `-0` from positive zero `+0`!

---

## Why This Exists
JavaScript has four equality comparison algorithms defined in the ECMAScript specification:
1. Abstract Equality (`==` / `Abstract Equality Comparison`)
2. Strict Equality (`===` / `Strict Equality Comparison`)
3. **SameValue** (`Object.is()`)
4. **SameValueZero** (Used internally by `Map`, `Set`, `Array.prototype.includes`)

Strict equality `===` follows the IEEE-754 floating-point specification, leading to counter-intuitive quirks: `NaN === NaN` returns `false` (making `NaN` the only value in JavaScript not equal to itself), and `-0 === +0` returns `true`. `Object.is()` was introduced in ES6 to provide a clean, predictable equality algorithm without IEEE-754 edge-case anomalies.

---

## Mental Model
Think of equality checks as scales:
- **Loose Scale (`==`):** Coerces different items (e.g. converting a string `"5"` to number `5`) before weighing them.
- **Strict Scale (`===`):** Refuses to weigh different types. However, it fails on `NaN` (claims two identical bags of unmeasurable dust `NaN` are different) and misses negative sign charges (`-0 === +0`).
- **`Object.is()` Scale (SameValue):** A precision lab balance scale. Identical `NaN` values weigh equal, and negative magnetic charge (`-0`) is recognized as distinct from positive charge (`+0`).

```
EQUALITY ALGORITHM COMPARISON FOR TRICKY CORNER CASES

NaN vs NaN:
NaN === NaN           ──► FALSE (IEEE-754 Quirk)
Object.is(NaN, NaN)   ──► TRUE  (SameValue Precision)

-0 vs +0:
-0 === +0             ──► TRUE  (IEEE-754 Quirk)
Object.is(-0, +0)     ──► FALSE (SameValue Precision)
```

---

## How It Works

`Object.is(value1, value2)` evaluates equality according to the ECMAScript **SameValue** algorithm:

1. **Type Check:** If `typeof value1 !== typeof value2`, return `false`.
2. **Number Evaluation:**
   - If both are `NaN`, return `true` (Unlike `===`).
   - If one is `+0` and the other is `-0`, return `false` (Unlike `===`).
   - For all other numbers, if values match, return `true`.
3. **Object & Symbol Evaluation:** Compares memory reference pointers (same identity = `true`).
4. **Primitives Evaluation:** Compares raw values.

---

## Key Characteristics
- **`NaN` Predictability:** `Object.is(NaN, NaN)` evaluates to `true`, simplifying math validation logic without requiring `Number.isNaN()`.
- **Signed Zero Detection:** Distinguishes `-0` from `+0`, which is critical in mathematical computations, financial engines, and 3D graphics algorithms (e.g., detecting vector direction or division by zero resulting in `-Infinity` vs `+Infinity`).

---

## Common Mistakes

### Mistake 1: Confusing `SameValue` (`Object.is`) with `SameValueZero` (`Map`/`Set`)
`Map` and `Set` use **SameValueZero** equality. SameValueZero treats `NaN === NaN` as `true` (like `Object.is`), but treats `-0` as equal to `+0` (like `===`).

```javascript
// Map uses SameValueZero:
const map = new Map();
map.set(-0, "Zero");

console.log(map.has(+0)); // Output: true! (SameValueZero considers -0 and +0 EQUAL)
console.log(Object.is(-0, +0)); // Output: false! (SameValue considers -0 and +0 DISTINCT)
```

---

## Best Practices
- **Use `Object.is()` when checking for `NaN` or signed zero (`-0`)** in algorithms, math engines, or state comparison utilities.
- **Use `===` for everyday application logic** where `-0` vs `+0` distinction is irrelevant.
- **Understand `SameValueZero`** when working with `Map`, `Set`, and `Array.prototype.includes()`.

---

## Comparison Table

| Corner Case Comparison | Loose Equality (`==`) | Strict Equality (`===`) | `Object.is()` (SameValue) | `Map`/`Set` (SameValueZero) |
| :--- | :--- | :--- | :--- | :--- |
| **`NaN` vs `NaN`** | `false` | `false` | **`true`** | **`true`** |
| **`-0` vs `+0`** | `true` | `true` | **`false` (Distinct)** | `true` (Equal) |
| **`"5"` vs `5`** | `true` (Coerced) | `false` | `false` | `false` |
| **`null` vs `undefined`** | `true` | `false` | `false` | `false` |
| **`{} vs {}`** | `false` | `false` | `false` | `false` |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates Object.is(), IEEE-754 quirks with NaN and signed zero,
 * and polyfilling Object.is() via the SameValue spec algorithm.
 */

// 1. NaN Equality Checks
const calcResult1 = 0 / 0; // NaN
const calcResult2 = Number("invalid_number"); // NaN

console.log("Strict NaN Check (calcResult1 === calcResult2):", calcResult1 === calcResult2); 
// Expected: false (IEEE-754 Quirk!)

console.log("Object.is NaN Check:", Object.is(calcResult1, calcResult2)); 
// Expected: true (SameValue Precision)

// 2. Signed Zero (-0 vs +0) Detection
const positiveZero = +0;
const negativeZero = -0;

console.log("Strict Zero Check (+0 === -0):", positiveZero === negativeZero); 
// Expected: true (IEEE-754 treats as equal)

console.log("Object.is Zero Check:", Object.is(positiveZero, negativeZero)); 
// Expected: false (SameValue recognizes sign distinction!)

// Mathematical impact of Signed Zero:
console.log("1 / +0:", 1 / positiveZero); // Expected: Infinity
console.log("1 / -0:", 1 / negativeZero); // Expected: -Infinity

// 3. Polyfill Implementation of Object.is() (SameValue Specification)
function sameValuePolyfill(x, y) {
  if (x === y) {
    // Check for -0 vs +0: 1/x === 1/y returns -Infinity vs +Infinity
    return x !== 0 || 1 / x === 1 / y;
  }
  // Check for NaN vs NaN: NaN is the only value where x !== x evaluates to true
  return x !== x && y !== y;
}

console.log("Polyfill NaN Check:", sameValuePolyfill(NaN, NaN));   // Expected: true
console.log("Polyfill Signed Zero Check:", sameValuePolyfill(+0, -0)); // Expected: false
console.log("Polyfill Normal Match:", sameValuePolyfill("abc", "abc")); // Expected: true
```

---

## Key Takeaways
- `Object.is()` implements the ECMAScript **SameValue Algorithm** for precise equality comparisons.
- `Object.is(NaN, NaN)` returns `true`, fixing strict equality's `NaN === NaN` (`false`) quirk.
- `Object.is(+0, -0)` returns `false`, recognizing signed zero distinctions.
- `Map`, `Set`, and `includes()` use **SameValueZero** equality (which treats `NaN === NaN` as `true` but `-0 === +0` as `true`).

---

## Related
- [[js-object-identity]] — Reference equality vs structural comparison.
- [[js-map-vs-object]] — SameValueZero equality inside Map key lookups.
- [[js-floating-point-math-quirks]] — IEEE-754 precision quirks in JavaScript numbers.
- [[MOC - JS Type Conversion & Equality]] — Map of Content for Equality Algorithms.
