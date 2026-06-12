---
id: 202602081302
title: Map, Filter, and Reduce
aliases: [Array Map, Array Filter, Array Reduce]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/arrays
date_created: {{date}}
mastery_level: 1
---
# Map, Filter, and Reduce

Known as the "Holy Trinity" of JavaScript array methods, these are Higher-Order Functions that allow for declarative, non-mutating data manipulation. 

## 1. `map()`
**Purpose:** Transforms every element in an array.
**Returns:** A *new array* of the exact same length as the original.
```javascript
const numbers = [1, 2, 3];
const doubled = numbers.map(num => num * 2);
// doubled is [2, 4, 6]
```
## 2. `filter()`

**Purpose:** Selects elements that pass a specific condition. **Returns:** A _new array_ containing only the elements where the callback returned a truthy value (can be shorter than original).

```JavaScript
const ages = [12, 18, 25, 8];
const adults = ages.filter(age => age >= 18);
// adults is [18, 25]
```
## 3. `reduce()`

**Purpose:** Accumulates all array elements into a single value (can be a number, string, object, or even another array). **Returns:** The final accumulated value.

```JavaScript
const cartPrices = [10, 20, 30];
const total = cartPrices.reduce((accumulator, currentVal) => accumulator + currentVal, 0);
// total is 60 (0 is the starting value of the accumulator)
```

## Method Chaining

Because `map` and `filter` return new arrays, you can elegantly chain them together:

```JavaScript
const finalResult = data
  .filter(user => user.isActive)
  .map(user => user.name);
```
## Related
- [[js-higher-order-functions-and-callbacks]] - The underlying mechanism for these methods.
- [[js-array-methods-mutating-vs-non-mutating]] - Why returning new arrays is safer.