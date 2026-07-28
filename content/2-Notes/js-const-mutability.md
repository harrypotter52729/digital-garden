---
id: 202601282120
title: JavaScript Const vs Mutability
aliases: []
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/memory
date_created: 2026-01-28
date_modified: 2026-01-28
mastery_level: 1
---
## JavaScript Const vs Mutability

Const is assumed to make the values of the variable immutable.

**True** - for primitive datatypes
**False** - for objects

- Const prevents variables from ****reassignment****
- creates **immutable binding**.
	- if we assign a reference type(Array etc.), it store address of the actual data.
	- here it prevents from changing address of the variable.
	- actual data can be changed. i.e., contents of array/obj 
- for primitive types actual data is stored, so it's immutable and cannot be reassigned


## Related
[[js-var-let-const]], [[js-primitive-vs-reference-types]]
