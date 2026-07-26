---
id: 202602081005
title: MOC - JS Objects & Structures
aliases: [JS Objects MOC]
tags:
  - type/MOC
  - lang/js
  - status/processing
  - topic/objects
date_created: {{date}}
mastery_level: 1
---
# MOC - JS Objects & Structures

This map acts as a hub for understanding how JavaScript constructs, unpacks, and manages complex data structures.

## Core Concepts
* [[js-object-creation]] - Syntax and patterns for creating objects.
* [[js-destructuring]] - Unpacking values from arrays or properties from objects into distinct variables.

## Array Structure & Mechanics
* [[js-array-specialized-objects]] - Array object model, numeric keys vs named properties.
* [[js-array-sparse-vs-dense-holes]] - Dense vs sparse holey arrays and empty slots vs undefined.
* [[js-array-length-mechanics]] - Highest index mechanics, dynamic expansion, and length truncation.
* [[js-array-creation-methods]] - Array literals, constructors, Array.of(), and Array.from().
* [[js-array-access-at-and-optional-chaining]] - Indexing, negative offset via at(), and safe optional chaining.
* [[js-array-push-pop-shift-unshift-splice]] - Mutating array operations, return values, and O(1) vs O(n) re-indexing.
* [[js-array-modern-immutable-methods]] - ES2023 non-mutating methods (toSorted, toReversed, toSpliced, with).
* [[js-array-iterators-keys-values-entries]] - Iterable protocol, Symbol.iterator, keys(), values(), and entries().

## Iteration & Manipulation
*(Concepts bridging Objects and Control Flow)*
* [[js-for-in-legacy]] - Looping over object properties.
* [[js-iterable-vs-enumerable]] - Understanding what makes object properties accessible.
* [[js-array-foreach-vs-map-filter]] - Iteration semantics (forEach, map, filter).
* [[js-array-reduce-and-reduceright]] - Aggregation using reduce and reduceRight.
* [[js-array-flat-and-flatmap]] - Flattening nested arrays.
* [[js-array-searching-includes-vs-indexof-find]] - Value and condition searching APIs.
* [[js-array-holes-in-methods]] - Hole handling algorithms across ECMAScript methods.
* [[js-array-typed-arrays-and-arraybuffer]] - Typed arrays, ArrayBuffer, and DataView binary memory.
* [[js-array-algorithmic-patterns]] - Deduplication, frequency counting, two pointers, sliding window, and prefix sums.

---
**Related MOCs:**
* [[MOC - JS Data Types & Memory]] (How objects are stored in the Heap)
* [[MOC - JS Built-in Objects & Utilities]] (Standard built-in object utilities)