---
id: 202602081103
title: Closures and Lexical Environment
aliases: [Closures]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/functions
date_created: {{date}}
mastery_level: 1
---
# Closures and Lexical Environment

A **Closure** is arguably the most powerful concept in JavaScript. It occurs when a function "remembers" its lexical scope even after the function outside of it has finished executing.

## How it Works
When a function is declared, it keeps a hidden reference to its **Lexical Environment** (the variables surrounding it). If you return an inner function from an outer function, that inner function maintains access to the outer function's variables, effectively keeping them alive in memory.

## Code Example: Data Privacy
Closures are frequently used to create private variables.

```javascript
function createCounter() {
  let count = 0; // 'count' is private

  return function() {
    count++;
    return count;
  };
}

const myCounter = createCounter();
console.log(myCounter()); // 1
console.log(myCounter()); // 2
// There is no way to directly access or modify 'count' from the outside!
```

## Related

- [[js-var-let-const]] - How block scope interacts with closures.
- [[js-higher-order-functions-and-callbacks]] - Closures are heavily used in HOFs.