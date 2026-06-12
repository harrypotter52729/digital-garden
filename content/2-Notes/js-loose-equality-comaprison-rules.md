---
id: 202602032224
title: js-loose-equality-comaprison-rules
aliases: []
tags:
  - type/reference
  - lang/js
  - status/processing
  - topic/conversion
date_created: 2026-02-03
date_modified: 2026-02-03
mastery_level: 1
---
## js-loose-equality-comparison-rules


- **Strings and Booleans** are mostly converted to **Numbers**
- if the string cant be converted to number, it will be converted to **NaN**
	- `true == "1"` → `1 == "1"` → `1 == 1` → `true`
	- `"42" == 42` → `42 == 42` → `true`

- **Null and Undefined**: These are loosely equal to each other but **not** equal to any other value in the language
 - `null == undefined` → `true`
 - `null == 0` → `false`

- If one side is **object** and other side is **primitive** then the object will  be converted to **primitive** 

- ==`NaN == NaN` is always **false**==

- For ==Objects the address will be compared==, not the contents

- **Empty Arrays**: `[]` converts to an empty string `""`, which then becomes `0` in a numeric context. This is why `[] == 0` is `true`.

- if both are same types no conversion occurs. , `"" == "0"` is **false** 