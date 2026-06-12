---
id: 202602032210
title: js-strict-vs-loose-equality
aliases: []
tags:
  - type/concpet
  - lang/js
  - status/processing
  - topic/comparison
date_created: 2026-02-03
date_modified: 2026-02-03
mastery_level: 1
---
## js-strict-vs-loose-equality


difference between them how they handle datatypes and type comparison.

### Strict equality

- it does not perform type comparison.
- If values is of different types it returns `false`
- it only returns `true` if both both match type and value.

### Loose equality

-  it performs [[js-type-conversion]]
- comparison rules while checking equality [[js-loose-equality-comaprison-rules]]