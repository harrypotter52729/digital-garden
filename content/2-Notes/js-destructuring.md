---
id: 202601282220
title: js-destructuring
aliases: []
tags:
  - type/snippet
  - lang/js
  - status/processing
  - topic/syntax
date_created: 2026-01-28
date_modified: 2026-01-28
mastery_level: 1
---
## js-destructuring


To unpack values from arrays, object properties and assign them to different variables.

## Object Destructuring 

**Rule:** Variable names must match the object keys (unless aliased). 
```js
 const user = { id: 42, name: "Sam", role: "Admin" }; // Unpack specific properties 
 const { name, role } = user; 
 console.log(name); // "Sam"
```


## Array destructuring

**Rule:** Unpacking is based on **order/index**, not names.
```js
const colors = ["red", "blue", "green"]; // Unpack by position 
const [primary, secondary] = colors; 
console.log(primary); // "red"
```
