---
id: 202602081105
title: IIFE (Immediately Invoked Function Expression)
aliases: [IIFE]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/functions
date_created: {{date}}
mastery_level: 1
---
# IIFE (Immediately Invoked Function Expression)

An IIFE (pronounced "iffy") is a JavaScript function that runs as soon as it is defined.

## Syntax
It requires wrapping the function in parentheses `()` to tell the JS Engine to treat it as an expression, followed by a second pair of parentheses `()` to immediately execute it.

```javascript
(function() {
  const secret = "I run immediately!";
  console.log(secret);
})();
```

(You can also use arrow functions: (() => { ... })();)

## Purpose & Use Cases
- Before let, const, and ES6 Modules existed, IIFEs were the primary way to prevent Global Namespace Pollution.

- Because var is function-scoped (not block-scoped), creating an IIFE ensured that variables declared inside it would not leak into the global window object, preventing naming collisions in large codebases.

Today, while less common due to modern modules and block scoping, they are still used for top-level async/await execution and specific data-hiding patterns.

## Related
[[js-var-let-const]] - Why var made IIFEs necessary.
[[js-function-declaration-vs-expression]]
[[js-closures-and-lexical-environment]] - IIFEs are often used to initialize closures.