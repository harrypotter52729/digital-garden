---
id: 202602030745
title: js-short-circuting
aliases: []
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/execution
date_created: 2026-02-03
date_modified: 2026-02-03
mastery_level: 1
---
## js-short-circuting


it is a behavior, while evaluating an logical expression it stops proceeding when the result is determined

**Logical OR ( || ):** if the first value is true the result will be true irrespective of the next one
**Logical AND (&&):** if the first value is false, result will be false.

**Nullish Coalescing :**  similar to || It only short-circuits if the value is **not** `null` or `undefined`

[[js-difference-between-or-operator-and-nullish-coalescing]] - Difference between the OR operator and nullish coalescing