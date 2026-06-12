---
id: 202602081304
title: Map and WeakMap
aliases: [Maps, Dictionary]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/collections
date_created: {{date}}
mastery_level: 1
---
# Map and WeakMap

While standard JavaScript Objects are used for key-value storage, they have a major limitation: **Object keys can only be Strings or Symbols**. 

A `Map` is an advanced collection that allows **any data type** (including Objects, Functions, and Arrays) to be used as a key.

## Standard `Map`
Maps also remember the exact insertion order of the keys, and are inherently iterable.
```javascript
const myMap = new Map();
const objKey = { id: 1 };

myMap.set(objKey, "Stored Data tied to Object");
myMap.set("regularString", 100);

console.log(myMap.get(objKey)); // "Stored Data tied to Object"
console.log(myMap.size); // 2
```

## `WeakMap`

Similar to `WeakSet`, `WeakMap` is designed for memory management.

1. **Keys must be Objects:** You cannot use primitive values as keys.
2. **Weakly Held Keys:** If the object used as the key is deleted/garbage collected elsewhere in the app, the entire key-value pair is automatically removed from the WeakMap.
3. **Not Iterable:** Cannot be looped over; has no `.size` property.

**Use Case:** WeakMaps are often used to attach private data or cache data to DOM elements or objects without causing memory leaks. If the DOM element is removed from the screen, the cached data in the WeakMap vanishes automatically.

## Related

- [[js-object-creation]] - The standard string-based key-value store.
- [[js-set-and-weakset]] - The unique-value collection equivalent.
- [[js-for-of-iterator]] - How to loop over Maps.