---
id: 202602081003
title: MOC - JS Type Conversion & Equality
aliases: [Coercion MOC, JS Equality]
tags:
  - type/MOC
  - lang/js
  - status/processing
  - topic/coercion
date_created: {{date}}
mastery_level: 1
---
# MOC - JS Type Conversion & Equality

This map organizes the rules of how JavaScript changes data types on the fly (coercion) and how it evaluates truthiness and equality.

## Type Conversion (Coercion)
* [[js-type-conversion]] - Overview of changing data from one type to another.
* [[js-explicit-type-conversion-methods]] - Manually converting types (e.g., `Number()`, `String()`).
* [[js-implicit-coersion]] - When the JS engine automatically converts types.
* [[js-operator-overloading-and-string-dominance]] - How the `+` operator behaves differently than `-`, `*`, or `/`.

## Equality & Evaluation
* [[js-falsy-values]] - The specific values that evaluate to `false` in a boolean context.
* [[js-strict-vs-loose-equality]] - The difference between `==` and `===`.
* [[js-loose-equality-comaprison-rules]] - The specific algorithm JS uses when evaluating `==`.

---
**Related MOCs:**
* [[MOC - JS Logic & Conditionals]] (Applying truthiness to application logic)