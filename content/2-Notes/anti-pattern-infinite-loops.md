---
id: 202602080855
title: anti-pattern-infinite-loops
aliases: []
tags:
  - type/error
  - lang/js
  - status/processing
  - topic/debugging
date_created: 2026-02-08
date_modified: 2026-02-08
mastery_level: 1
---
## anti-pattern-infinite-loops


## Definition
An infinite loop occurs when the termination condition never evaluates to `false`, causing the program to hang or crash the browser tab.

## Common Causes

### 1. Failing to Update
In `while` loops, forgetting to increment the counter inside the body is the most frequent cause.
```javascript
while (i < 10) {
  console.log(i);
  // Missing i++ here causes infinite loop
}
```
### 2. Incorrect Direction

Updating the counter in the wrong direction (e.g., decrementing `i--` when the condition checks `i < 10`) ensures the condition is always true.

### 3. Variable Mix-ups

In nested loops, using the outer loop's variable (e.g., `i`) inside the inner loop allows the outer loop to reset or stall.

### 4. Unreachable Break

Using `while(true)` but placing the `break` statement inside a code path that is never reached (e.g., inside a `try/catch` that suppresses the trigger).