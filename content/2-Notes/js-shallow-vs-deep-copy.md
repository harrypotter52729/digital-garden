---
id: 202601282138
title: js-shallow-vs-deep-copy
aliases:
  - structuredClone
  - Spread Operator
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/data-structures
date_created: 2026-01-28
date_modified: 2026-01-28
mastery_level: 1
---
## js-shallow-vs-deep-copy


In JS objects are stored by the reference
see [[js-primitive-vs-reference-types]], 
* if we simply assign a value `b=a` it copies the address, pointing to the same data, 
* we should explicitly copy data if we want to create completely independent copy.

### Shallow Copy 

Creates a new object, but only copies values at the **first level**. 
* **Primitives:** Copied by value. 
* **Nested Objects:** The *reference* is copied. if your object contains another object inside it (nested data) then address is shared.
* **Methods:** `Object.assign({}, original)` or Spread Syntax `{ ...original }`.

### Deep Copy 

Recursively copies every level of the object tree. The new object is 100% disconnected from the original.
* **Modern Method:** `structuredClone(obj)`
* .**Legacy Method:** `JSON.parse(JSON.stringify(obj))` (Note: This fails with Dates, Functions, and `undefined`).

## Related

- [[js-primitive-vs-reference-types]]
