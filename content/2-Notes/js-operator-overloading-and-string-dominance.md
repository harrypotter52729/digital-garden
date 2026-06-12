---
id: 202601300737
title: js-operator-overloading-and-string-dominance
aliases: []
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/conversion
date_created: 2026-01-30
date_modified: 2026-01-30
mastery_level: 1
---
## js-operator-overloading-and-string-dominance


### The '+' Operator

- In JS `+` operator performs both **addition** and **concatenation**. (Operator overloading)

### String dominance

- If any operand is String, the js coerces the other operands to string and concatenates.
- String > Number.
- **Example:** `'5' + 3` results in `"53

### Other operators (-,*, /, %)

- unlike '+' these exists only in maths , so js *converts the string to numbers.*
- Number > String
- **Example:** `'5' - '2'` results in `3` 