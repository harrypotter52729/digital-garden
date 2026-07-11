---
id: 202602080802
title: MOC - JS control flow
aliases: []
tags:
  - type/MOC
  - lang/js
  - status/processing
  - topic/control-flow
date_created: 2026-02-08
date_modified: 2026-02-08
mastery_level: 1
---
# MOC - JS control flow

This map covers how JavaScript handles repeated execution of code blocks through looping mechanisms.

## Fundamental Loops
* [[js-for-loop-syntax]] - Standard counter-based iteration.
* [[js-while-loop-pattern]] - Condition-based iteration (pre-test).
* [[js-do-while-loop]] - Post-test iteration (guaranteed execution).

## Modern Iteration (ES6+)
* [[js-for-of-iterator]] - The modern standard for values (Arrays, Strings, Maps).
* [[js-for-in-legacy]] - Legacy iteration for object keys (and why to avoid it).

## Conceptual Patterns & Best Practices
* [[js-loop-selection-strategy]] - When to use `for` vs `while`.
* [[js-iterable-vs-enumerable]] - The semantic difference between `for...of` and `for...in`.
* [[js-anti-pattern-infinite-loops]] - Common pitfalls and memory leaks.

---
**Related MOCs:**
* [[MOC - JS Logic & Conditionals]] (Decision making inside and outside of loops)
* [[MOC - JS Objects & Structures]] (What we are usually iterating over)