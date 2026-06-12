---
id: 202602080809
title: js-for-of-iterator
aliases: []
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/es6
date_created: 2026-02-08
date_modified: 2026-02-08
mastery_level: 1
---
## js-for-of-iterator

- Introduced in ES6
- it iterates over the values instead of indices
- simplifies the code by removing the need of manual counter.

## Supported Data Structures

It works on "Iterables":
* Arrays
* Strings (Correctly handles UTF-16/Emojis)
* Maps (Returns `[key, value]`)
* Sets
* `arguments` object

## Syntax vs Standard Loop

```js
const framework = "React";

// Concise, no manual index management
for (const char of framework) {
  console.log(char);
}
```

## Constraints

- **Plain Objects:** `for...of` does **not** work on plain JavaScript objects (`{a:1}`) because they are not iterable by default.
- **Workaround:** Use `Object.keys()`, `Object.values()`, or `Object.entries()` to convert the object data into an array first.
```js

  const user = { name: "Dev", id: 1 };
  // for (const prop of user) // Error: user is not iterable
  
  // Correct approach
  for (const [key, val] of Object.entries(user)) {
  console.log(key, val);
}
  ```