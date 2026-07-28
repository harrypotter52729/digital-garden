---
id: 202607281956
title: Fine-Grained Property Descriptors via Object.defineProperty()
aliases:
  - Object.defineProperty
  - property descriptors js
  - defineProperty
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/objects
  - topic/descriptors
date_created: {today}
mastery_level: 1
---

# Fine-Grained Property Descriptors via `Object.defineProperty()`

> **TL;DR:** `Object.defineProperty()` is like setting custom permission switches on a security vault compartment—allowing you to explicitly control whether a property can be overwritten (`writable`), deleted (`configurable`), or visible in enumeration loops (`enumerable`)!

---

## Why This Exists
Standard property assignments (`obj.key = value`) create properties that are writable, enumerable, and configurable by default. `Object.defineProperty()` provides complete control over an object property's internal metadata attributes, powering reactive state engines (like Vue 2) and immutable framework abstractions.

---

## The Four Property Attributes

1. **`value`**: The actual data value stored in the property.
2. **`writable`**: If `true`, the property value can be modified via assignment `=`.
3. **`enumerable`**: If `true`, the property appears in `for...in` loops and `Object.keys()`.
4. **`configurable`**: If `true`, the property can be deleted from the object and its descriptor attributes can be altered.

---

## Canonical Code Example

```javascript
/**
 * Demonstrates defining read-only, non-enumerable, non-configurable properties.
 */

const config = {};

// Define a read-only, hidden system property
Object.defineProperty(config, "API_KEY", {
  value: "SECRET_KEY_12345",
  writable: false,     // Immutable value
  enumerable: false,   // Hidden from Object.keys()
  configurable: false  // Cannot be deleted or redefined
});

console.log("API_KEY Value:", config.API_KEY); // Expected: "SECRET_KEY_12345"

// 1. Test Enumeration Protection
console.log("Object.keys():", Object.keys(config)); // Expected: [] (Hidden!)

// 2. Test Writable Protection in Strict Mode
try {
  config.API_KEY = "HACKED";
} catch (err) {
  console.log("Assignment Error:", err.message); // Expected: Cannot assign to read only property
}

// 3. Test Configurable Protection
try {
  delete config.API_KEY;
} catch (err) {
  console.log("Delete Error:", err.message); // Expected: Cannot delete property
}
```

---

## Key Takeaways
- Properties defined via `Object.defineProperty()` default to `false` for `writable`, `enumerable`, and `configurable`.
- Non-enumerable properties are excluded from `Object.keys()` but accessible via direct lookup or `Reflect.ownKeys()`.
- Once `configurable: false` is set, the property cannot be deleted or converted back.

---

## Related
- [[js-property-descriptors]] — Overview of accessor descriptors (getters/setters).
- [[js-object-freeze]] — Freezing all properties on an object.
- [[MOC - JS Objects & Structures]] — Objects MOC.
