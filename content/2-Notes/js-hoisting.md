---
id: 202601272106
title: Hoisting
aliases: []
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/execution
date_created: 2026-01-27
date_modified: 2026-01-27
mastery_level: 1
---
## Hoisting

>Before executing the code, the JS Engine **moves the declarations** to the top of code. this behavior is called Hoisting

It's not actually moves the code, but it create a declaration in memory

1. **var** initial value is undefined.
2. **let & const**  are not initialized and they will be store in [[js-temporal-dead-zone|Temporal Dead Zone]], so on execution we will get reference error

```js
console.log(foo); // undefined
var foo = 1; // here foo is moved up var foo; --first line
console.log(foo); // 1
```

```js
y; // ReferenceError: Cannot access 'y' before initialization
let y = 'local';
```

## Related
Hoisting will affect how the [[js-var-let-const]] will behave