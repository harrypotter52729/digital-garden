---
id: 202602080852
title: js-for-in-legacy
aliases: []
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/depricated
date_created: 2026-02-08
date_modified: 2026-02-08
mastery_level: 1
---
## js-for-in-legacy

## Definition
A loop designed to iterate over the **enumerable properties (keys)** of an object.

## The Prototype Chain Risk
Crucially, `for...in` traverses the prototype chain. It iterates over the object's own properties *and* any enumerable properties inherited from its parents.

### Example of Risk
```javascript
var object = { a: "foo", b: "bar" };
// If Object.prototype has a custom method, this loop will log it too!
for (var key in object) {
  console.log(key);
}
```

## Mitigation

If you must use this loop, you should wrap the body in a `hasOwnProperty` check to filter out inherited properties.

## Modern Alternatives

- **Arrays:** Use `for...of` instead. `for...in` does not guarantee order and returns indices as strings, not numbers.
- **Objects:** Use `Object.keys()` to get an array of own properties safely.

## related: 
[[js-for-of-iterator]]