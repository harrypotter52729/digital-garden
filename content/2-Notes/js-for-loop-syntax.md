---
id: 202602080804
title: js-for-loop-syntax
aliases: []
tags:
  - type/snippet
  - lang/js
  - status/processing
  - topic/control-flow
date_created: 2026-02-08
date_modified: 2026-02-08
mastery_level: 1
---
## js-for-loop-syntax


- To repeat a block of code a specific number of times.
- Use for loop if the number of iterations are known

### Syntax

```
for (initialization; condition; update) {
  // Code to run
}
```

## Control Flow Keywords

- **`break`**: Immediately terminates the loop.
- **`continue`**: Skips the current iteration and jumps to the **update** expression.

## Best Practices

- **Scope:** Always use `let` instead of `var`. `var` leaks the counter `i` into the global or function scope, causing potential collision bugs.
- **Performance:** For very large arrays, cache the length (`let len = arr.length`) in the initialization to avoid property access on every check, though modern engines optimize this heavily.

## Related
[[js-while-loop-pattern]] 