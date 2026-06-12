---
id: 202602081104
title: Higher-Order Functions and Callbacks
aliases: [HOF, Callbacks]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/functions
date_created: {{date}}
mastery_level: 1
---
# Higher-Order Functions and Callbacks

In JavaScript, functions are **First-Class Citizens**, meaning they can be treated exactly like standard variables (passed around, assigned, returned).

## Higher-Order Functions (HOF)
A Higher-Order Function is any function that does at least one of the following:
1. Takes one or more functions as arguments.
2. Returns a function as its result.

## Callbacks
A callback is the function that is *passed into* the Higher-Order Function to be executed later.

```javascript
// The Callback Function
function greet(name) {
  return `Hello, ${name}!`;
}

// The Higher-Order Function
function processUser(name, callback) {
  const result = callback(name);
  console.log("Processed:", result);
}

processUser("Bob", greet); // "Processed: Hello, Bob!"
```

## Why is this important?

This pattern is the foundation of asynchronous JavaScript (Promises/Event Listeners) and functional array methods (like `map`, `filter`, and `reduce`).

## Related

- [[js-function-declaration-vs-expression]]
- [[js-callbacks-and-inversion-of-control]] _(Future Note: Asynchronous JS)_
- [[js-map-filter-reduce]] _(Future Note: Arrays)_