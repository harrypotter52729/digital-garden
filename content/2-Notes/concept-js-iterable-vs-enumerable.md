---
id: 202602080815
title: concept-js-iterable-vs-enumerable
aliases: []
tags:
  - type/pattern
  - lang/js
  - status/processing
  - topic/interview-prep
date_created: 2026-02-08
date_modified: 2026-02-08
mastery_level: 1
---
## concept-js-iterable-vs-enumerable


# Comparison: For...Of vs For...In

A fundamental distinction in JavaScript is what constitutes "iteration."

## The Matrix

| Feature         | `for...in`                            | `for...of`                      |
| :-------------- | :------------------------------------ | :------------------------------ |
| **Target**      | **Keys** (Property Names)             | **Values** (Data)               |
| **Mechanism**   | Enumerability                         | Iterability (`Symbol.iterator`) |
| **Inheritance** | **Walks Prototype Chain** (Dangerous) | Own values only                 |
| **Use Case**    | Debugging, plain objects (rarely)     | Arrays, Strings, Maps, Sets     |

## The Prototype Trap (`for...in`)

`for...in` iterates over inherited properties. This is often considered a "bad part" of JS for arrays.

```javascript
Array.prototype.customMethod = function() {};
const list = ['a', 'b'];

for (const i in list) {
  console.log(i); // Outputs: "0", "1", "customMethod" -> UNEXPECTED
}
```

