---
id: 202607282022
title: Object & Array Destructuring Syntax & Patterns
aliases:
  - destructuring
  - object destructuring
  - array destructuring
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/syntax
date_created: 2026-07-28
mastery_level: 1
---

# Object & Array Destructuring Syntax & Patterns

> **TL;DR:** Destructuring is like an unpacking workstation—allowing you to unpack individual values from arrays or properties from objects into distinct variables using clean, inline matching syntax!

---

## Canonical Code Example

```javascript
/**
 * Demonstrates object and array destructuring with default values, 
 * property renaming, rest parameters, and nested unpacking.
 */

// 1. Object Destructuring with Renaming & Defaults
const user = {
  id: 101,
  username: "hemanth_dev",
  profile: { city: "Hyderabad" }
};

const { username: handle, role = "User", profile: { city } } = user;

console.log("Handle:", handle); // Expected: "hemanth_dev"
console.log("Role:", role);     // Expected: "User" (Default applied)
console.log("City:", city);     // Expected: "Hyderabad" (Nested extraction)

// 2. Array Destructuring with Skipping & Rest
const colors = ["red", "green", "blue", "yellow"];

const [primary, , tertiary, ...remaining] = colors;

console.log("Primary:", primary);     // Expected: "red"
console.log("Tertiary:", tertiary);   // Expected: "blue" (Skipped "green")
console.log("Remaining:", remaining); // Expected: ["yellow"]

// 3. Swapping Variables without Temporary Variable
let a = 1, b = 2;
[a, b] = [b, a];
console.log("Swapped:", a, b);        // Expected: 2 1
```

---

## Related
- [[js-spread-and-rest-operators]] — Rest and spread operators.
- [[js-optional-chaining-operator]] — Safe nested property access.
- [[MOC - JS Modern Syntax & Operators]] — Modern Syntax MOC.
