---
id: 202601300752
title: js-common-coersion
aliases: []
tags:
  - type/snippet
  - lang/english
  - status/processing
  - topic/conversion'
date_created: 2026-01-30
date_modified: 2026-01-30
mastery_level: 1
---
## js-implicit-coersion

### String Dominance (String Conversion)

```js
'5' + 3;     // "53" (Number 3 becomes string "3")
'5' + true;  // "5true"
5 + 5 + '5'; // "105" (Evaluates left to right: 5+5=10, then 10+'5')
```

### Mathematical Operators (`-`, `*`, `/`, `%`) (Number conversion)
```js
'5' - '2';   // 3
'5' * '2';   // 10
'5' / 2;     // 2.5
'Hello' - 1; // NaN (Cannot convert 'Hello' to a number)
```


## Boolean conversion
- occurs in if conditions and logical operations like || and  &&
- [[js-falsy-values]] - these types are converted to false
- other than these [],{} are also truthy values
- [[js-loose-equality-comaprison-rules]] - loose comparison coersion rules
