---
id: 202601290741
title: js-if-else-switch-conditional-execution
aliases:
  - if/else
  - switch
tags:
  - type/snippet
  - lang/js
  - status/processing
  - topic/execution
date_created: 2026-01-29
date_modified: 2026-01-29
mastery_level: 1
---
## js-if-else-switch-conditional-execution

## if-else

- run the block on code if **condition is true** (based on truthy values like 0,"")
- [[js-ternary-operator]] is short form of if else.

```js
let time = 14; 

if (time < 12) {
  console.log("Good morning!");
} else if (time < 18) {
  console.log("Good afternoon!"); // Runs because 14 is < 18
} else {
  console.log("Good evening!");
}

```


## Switch

- Uses **strict equality ( === )** for comparison.

```js
switch (expression) {
  case value1:
    // Code to run if expression === value1
    break;
  case value2:
    // Code to run if expression === value2
    break;
  default:
    // Code to run if no cases match
}

```

### what happens if we don't add break after case statement
JS checks for the condition that is matching, then it wil**l execute all the following case statements** if we don't write break.

## Related

- js converts the condition to binary using [[js-type-conversion]]
- [[js-difference-ifelse-and-switch]] when to use if else & switch.
- if else is short form of [[js-ternary-operator]] 
