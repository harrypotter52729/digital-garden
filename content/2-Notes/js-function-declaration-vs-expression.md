---
id: 202602081101
title: Function Declarations vs Expressions
aliases: [Function Declaration, Function Expression]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/functions
date_created: {{date}}
mastery_level: 1
---
# Function Declarations vs Expressions

In JavaScript, there are two primary ways to define a standard function, and they behave differently during the execution context creation phase.

## Function Declaration
A function defined with the `function` keyword standing on its own. 
* **Hoisting:** Declarations are **fully hoisted**. You can call the function before it is defined in the code.

```javascript
greet(); // "Hello!" - Works perfectly

function greet() {
  console.log("Hello!");
}
```


## Function Expression

A function that is assigned to a variable. It can be named or anonymous.

- **Hoisting:** The _variable_ is hoisted according to its keyword (`var`, `let`, `const`), but the _function assignment_ is not. You cannot call it before it is defined.
- 
JavaScript

```
sayHi(); // ReferenceError: Cannot access 'sayHi' before initialization (if using let/const)

const sayHi = function() {
  console.log("Hi!");
};
```

## Related

- [[js-hoisting]] - Deep dive into the creation phase and Temporal Dead Zone.
- [[js-arrow-functions-and-lexical-this]] - A modern alternative to function expressions.