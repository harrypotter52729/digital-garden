---
id: 202602080807
title: js-while-loop-pattern
aliases: []
tags:
  - type/null
  - lang/js
  - status/processing
  - topic/control-flow
date_created: 2026-02-08
date_modified: 2026-02-08
mastery_level: 1
---
## js-while-loop-pattern

## Concept
A control flow statement that repeatedly executes a block of code as long as a specified condition evaluates to `true`. It functions effectively as a "repeated `if` statement".

## Syntax

Unlike `for` loops, state management is manual. You must declare the variable *before* the loop and update it *inside* the loop.

```javascript
let number = 0; // Initialization
while (number <= 12) { // Condition check
  console.log(number);
  number = number + 2; // Manual Update
}
```

## Use Case

Use `while` loops when the number of iterations is **unknown** or depends on a dynamic condition (e.g., waiting for a user to input a correct password).

## Risks

If the update step is forgotten (e.g., `number` is never incremented), the condition remains `true` forever, causing an infinite loop.

## Related

[[js-do-while-loop]]