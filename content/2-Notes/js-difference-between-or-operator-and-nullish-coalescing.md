---
id: 202602032113
title: js-difference-between-or-operator-and-nullish-coalescing
aliases: []
tags:
  - type/snippet
  - lang/js
  - status/processing
  - topic/operators
date_created: 2026-02-03
date_modified: 2026-02-03
mastery_level: 1
---
## js-difference-between-or-operator-and-nullish-coalescing


### OR operator ||

OR operator converts the value to Boolean, if the left value is false and right value is true, it returns right value. 

[[js-falsy-values]] - 0 and ""(empty string) will be converted to false and returns, right value if true
this does not happen in nullish Coalescing

### Nullish coalesing ??

it returns the right value only if the left value is **null and undefined**

```js
console.log(0 || 100) //100
console.log(0 ?? 100) //0

console.log(null || 'user') //user
console.log(null ?? 'user') //user
```