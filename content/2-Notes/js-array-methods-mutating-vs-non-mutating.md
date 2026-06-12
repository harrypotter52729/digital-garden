---
id: 202602081301
title: Array Methods - Mutating vs Non-Mutating
aliases: [Mutating Arrays, Array Methods]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/arrays
date_created: {{date}}
mastery_level: 1
---
# Array Methods: Mutating vs Non-Mutating

When working with arrays in JavaScript, it is critical to know whether a method **mutates** (changes) the original array in memory, or if it is **non-mutating** (returns a brand new array, leaving the original intact).

## Mutating Methods (Destructive)
These change the underlying array in the Heap. If you have multiple variables pointing to this array, they will all see the changes.
* `push()` / `pop()` (Add/remove from end)
* `shift()` / `unshift()` (Add/remove from start)
* `splice()` (Add/remove at specific index)
* `sort()` (Sorts the original array in place)
* `reverse()` (Reverses the original array in place)
* `fill()` (Fills elements with a static value)

## Non-Mutating Methods (Safe)
These return a new value (usually a new array) and do not alter the original.
* `slice()` (Extracts a portion into a new array)
* `concat()` (Merges arrays together)
* `join()` (Returns a string)
* `includes()`, `indexOf()` (Returns boolean/number)
* **Functional Iterators:** `map()`, `filter()`, `reduce()`

## Modern JS Update (ES2023)
JavaScript recently introduced non-mutating versions of traditional mutating methods so you don't have to manually copy the array first:
* `toSorted()` (instead of `sort`)
* `toReversed()` (instead of `reverse`)
* `toSpliced()` (instead of `splice`)
* `with(index, value)` (instead of `arr[index] = value`)

## Related
* [[js-shallow-vs-deep-copy]] - Why mutating reference types can be dangerous.
* [[js-map-filter-reduce]] - The core non-mutating functional methods.