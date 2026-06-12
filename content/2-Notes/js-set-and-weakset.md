---
id: 202602081303
title: Set and WeakSet
aliases: [Sets]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/collections
date_created: {{date}}
mastery_level: 1
---
# Set and WeakSet

While Arrays are indexed collections, `Set` is an iterable collection of **unique values**. A Set cannot contain duplicates.

## Standard `Set`
Can store primitive values or object references. If you try to add a duplicate value, the Set simply ignores it.
```javascript
const mySet = new Set([1, 2, 2, 3, 3]);
console.log(mySet); // Set(3) { 1, 2, 3 }

mySet.add(4);
mySet.delete(1);
console.log(mySet.has(2)); // true
```

**Pro-Tip:** The most common use case for a Set is quickly removing duplicates from an array: `const uniqueArray = [...new Set(arrayWithDuplicates)];`

## `WeakSet`

A variation of Set with strict limitations designed for **memory optimization** (Garbage Collection).

1. **Objects Only:** A WeakSet can _only_ store object references, not primitives (no strings, numbers, etc.).
2. **Weakly Held References:** If no other variables reference an object stored inside a WeakSet, the garbage collector will delete it from memory entirely.
3. **Not Iterable:** Because the garbage collector can remove items at any time, you cannot loop over a WeakSet or use the `.size` property.

## Related

- [[js-map-and-weakmap]] - The key-value equivalent of Sets.
- [[js-primitive-vs-reference-types]] - Essential for understanding why WeakSet only takes objects.