---
id: 202607282020
title: Falsy Values & Boolean Coercion Rules in JavaScript
aliases:
  - falsy values
  - falsy js
  - boolean coercion falsy
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/types
  - topic/logic
date_created: 2026-07-28
mastery_level: 1
---

# Falsy Values & Boolean Coercion Rules in JavaScript

> **TL;DR:** JavaScript has exactly 8 falsy values—values that evaluate to `false` when cast to a boolean context (like an `if` condition). Every other value in the language (including empty arrays `[]` and empty objects `{}`) evaluates to `true`!

---

## The Canonical 8 Falsy Values

1. `false` (Boolean literal)
2. `0`, `-0`, `0n` (Zero numbers & BigInt zero)
3. `""`, `''`, `\`\` (Empty string)
4. `null` (Intentional absence of object)
5. `undefined` (Uninitialized value)
6. `NaN` (Not-a-Number)
7. `document.all` (Legacy browser host object artifact)

---

## Common Gotchas & Truthy Traps
- `[]` (Empty Array) -> **TRUTHY!**
- `{}` (Empty Object) -> **TRUTHY!**
- `"0"` (String containing zero) -> **TRUTHY!**
- `"false"` (String containing "false") -> **TRUTHY!**

---

## Canonical Code Example

```javascript
/**
 * Demonstrates Boolean coercion and common truthy gotchas in JavaScript.
 */

function checkTruthy(val) {
  return Boolean(val);
}

// 1. All Falsy Values
console.log("Boolean(0):", checkTruthy(0));             // Expected: false
console.log("Boolean(''):", checkTruthy(''));           // Expected: false
console.log("Boolean(null):", checkTruthy(null));       // Expected: false
console.log("Boolean(undefined):", checkTruthy(undefined)); // Expected: false
console.log("Boolean(NaN):", checkTruthy(NaN));         // Expected: false

// 2. Traps: Seemingly falsy, but actually Truthy!
console.log("Boolean([]):", checkTruthy([]));           // Expected: true!
console.log("Boolean({}):", checkTruthy({}));           // Expected: true!
console.log("Boolean('0'):", checkTruthy('0'));         // Expected: true!
console.log("Boolean('false'):", checkTruthy('false')); // Expected: true!

// Array length check vs raw array check
const items = [];

// BAD: items resolves to truthy!
if (items) {
  console.log("This executes even though array is empty!");
}

// GOOD: Explicit length check
if (items.length > 0) {
  console.log("Array has elements.");
}
```

---

## Related
- [[js-null-vs-undefined]] — Differences between null and undefined.
- [[js-type-conversion]] — Implicit vs explicit type conversions.
- [[MOC - JS Type Conversion & Equality]] — Type Conversion MOC.
