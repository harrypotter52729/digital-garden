---
id: 202602081102
title: Arrow Functions and Lexical this
aliases: [Arrow Functions, Lexical Scope]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/functions
date_created: {{date}}
mastery_level: 1
---
# Arrow Functions and Lexical `this`

Introduced in ES6, arrow functions provides shorter way to write function expressions. However, their most important feature is how they handle the `this` keyword.

## Syntax Differences
* Implicit returns (no `return` keyword needed for single-line expressions).
* No `function` keyword.

```javascript
 // Standard Expression
  const add = function(a, b) { return a + b; }; 
  // Arrow Function 
  const addArrow = (a, b) => a + b;
```

## Lexical `this` (The Big Difference)

Standard functions create their own `this` context depending on _how_ they are called. Arrow functions **do not have their own `this`**. 

Instead, they inherit `this` from the parent scope at the time they are defined (Lexical Scoping).


```JavaScript
const user = {
  name: "Alice",
  regularFunc: function() {
    console.log(this.name); // "Alice"
  },
  arrowFunc: () => {
    console.log(this.name); // undefined (points to the global/window object)
  }
};
```

_Note: Arrow functions also do not have access to the `arguments` object._

## Related

- [[js-function-declaration-vs-expression]]
- [[js-the-this-keyword-rules]] _(Future Note)_