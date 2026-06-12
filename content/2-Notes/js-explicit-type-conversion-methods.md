---
id: 202601300746
title: js-explicit-type-conversion-methods
aliases: []
tags:
  - type/snippet
  - lang/js
  - status/processing
  - topic/conversion
date_created: 2026-01-30
date_modified: 2026-01-30
mastery_level: 1
---
## js-explicit-type-conversion-methods


Methods for safely converting values between types (Type Casting).

## To string

```js
String(123);       // "123"
String(true);      // "true"
String(null);      // "null"
(123).toString();  // "123"
```


## To Number

```js
Number("123");     // 123
Number("3.14");    // 3.14
Number("");        // 0  (Empty string becomes 0)
Number(true);      // 1
Number(false);     // 0
Number("Hello");   // NaN (Not a Number)
```
## To Boolean

```js
Boolean(1);        // true
Boolean(0);        // false
Boolean("Hello");  // true
	```