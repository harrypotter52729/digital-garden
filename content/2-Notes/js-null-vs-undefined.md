---
id: 202607282021
title: Null vs Undefined Mechanics & Differentials
aliases:
  - null vs undefined
  - undefined vs null
  - typeof null bug
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/types
date_created: 2026-07-28
mastery_level: 1
---

# Null vs Undefined Mechanics & Differentials

> **TL;DR:** `undefined` is JavaScript's default signal meaning "a variable has been declared but not assigned a value," while `null` is an explicit developer signal meaning "this variable intentionally points to empty space or no object!"

---

## Comparison Table

| Attribute | `undefined` | `null` |
| :--- | :--- | :--- |
| **Meaning** | Uninitialized / System default | Intentional emptiness / Developer assignment |
| **Type (`typeof`)** | `"undefined"` | `"object"` (Historical JS 1.0 engine bug!) |
| **Number Coercion** | `Number(undefined)` -> `NaN` | `Number(null)` -> `0` |
| **JSON Serialization**| Omitted from object properties | Preserved as `null` literal |
| **Default Parameters**| Triggers default function args | Bypasses default function args |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates null vs undefined differentials in type checks, 
 * default parameters, and JSON serialization.
 */

// 1. Type Check Quirks
console.log("typeof undefined:", typeof undefined); // Expected: "undefined"
console.log("typeof null:", typeof null);           // Expected: "object" (Legacy bug)

// 2. Equality Comparisons
console.log("null == undefined:", null == undefined);   // Expected: true (Loose equality)
console.log("null === undefined:", null === undefined); // Expected: false (Strict equality)

// 3. Default Parameter Behavior
function greet(name = "Guest") {
  return `Hello, ${name}`;
}

console.log("greet(undefined):", greet(undefined)); // Expected: "Hello, Guest" (Triggers default!)
console.log("greet(null):", greet(null));           // Expected: "Hello, null" (Does NOT trigger default!)

// 4. JSON Serialization
const payload = {
  unassigned: undefined,
  empty: null
};

console.log("JSON.stringify():", JSON.stringify(payload)); // Expected: '{"empty":null}' (undefined key dropped!)
```

---

## Related
- [[js-falsy-values]] — Boolean coercion of null and undefined.
- [[js-primitive-vs-reference-types]] — Primitive types overview.
- [[MOC - JS Data Types & Memory]] — Data Types MOC.
