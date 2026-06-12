---
id: 202601262134
title: JS Variables
aliases: []
tags:
  - type/reference
  - lang/js
  - status/processing
  - topic/syntax
date_created: 2026-01-26
date_modified: 2026-01-26
mastery_level: 1
source: ""
---
Variables are the named containers to store data.

**Legacy:** var
**Standard**: let vs const

They differ in behavior, like scope, Hoisting ,Mutability, Scope.

|                   | var                   | let                              | const                            |
| ----------------- | --------------------- | -------------------------------- | -------------------------------- |
| **Scope**         | function-scoped       | block-scoped                     | block-scoped                     |
| **Reassignment**  | allowed               | allowed                          | not allowed                      |
| **Redeclaration** | allowed in same scope | not allowed                      | not allowed                      |
| **Hoisting**      | undefined             | Hoisted, but uninitialized (TDZ) | Hoisted, but uninitialized (TDZ) |

## Related
[[js-hoisting]] - how these declaration behave during creation phase
[[js-const-mutabilty]] - const and immutable misunderstoop concepts



