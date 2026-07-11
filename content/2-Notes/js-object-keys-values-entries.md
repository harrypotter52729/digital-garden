---
id: 202606120837
title: Object.keys, values, entries
aliases:
  - object-methods
  - object-iteration
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/objects
date_created: 2026-06-12
mastery_level: 1
---

# Object.keys, values, entries

> **TL;DR:** Converting objects to arrays is like taking a **labeled chest of drawers** and dumping either just the labels (`Object.keys`), just the contents (`Object.values`), or pairs of labels and items (`Object.entries`) onto a conveyor belt (an array) so you can process them one by one.

## The Mental Model
Objects are great for looking up items by a specific name (e.g., `user.email`), but they are bad at loops. You cannot run functional array methods like `.map()`, `.filter()`, or `.reduce()` directly on a raw object.

To solve this, JavaScript provides three static methods to transform objects into arrays, opening the door to all standard array iteration features.

---

## The Big Three Transformation Methods

Given the object:
```javascript
const user = { name: "Hemanth", role: "Developer" };
```

### 1. `Object.keys(user)`
- **What it returns:** An array of the object's own string keys.
- **Output:** `["name", "role"]`

### 2. `Object.values(user)`
- **What it returns:** An array of the object's values.
- **Output:** `["Hemanth", "Developer"]`

### 3. `Object.entries(user)`
- **What it returns:** A multi-dimensional array of key-value pairs (tuples).
- **Output:** `[["name", "Hemanth"], ["role", "Developer"]]`
- *Tip: This is perfect for combined [[js-destructuring|destructuring]] loops.*

---

## Reversing the Transformation: `Object.fromEntries()`
If you have an array of key-value pairs (like those created by `Object.entries`), you can convert them back into a structured object using `Object.fromEntries(nestedArray)`. This is highly useful when you want to filter or modify object keys using array methods and then rebuild the original object shape.

---

## Canonical Code Example

Here is a script showing how to use these methods to iterate, calculate totals, and filter objects using array methods:

```javascript
const inventory = {
  apples: 10,
  bananas: 25,
  cherries: 5,
  dates: 12
};

// 1. Using Object.keys() to list all items
const items = Object.keys(inventory);
console.log("We sell:", items.join(", ")); // "We sell: apples, bananas, cherries, dates"


// 2. Using Object.values() to calculate total stock
const totalStock = Object.values(inventory).reduce((total, count) => total + count, 0);
console.log("Total Stock Count:", totalStock); // 52


// 3. Using Object.entries() to loop with destructuring
// Each element is passed as [key, value] which we destructure in the argument list
for (const [fruit, qty] of Object.entries(inventory)) {
  console.log(`There are ${qty} ${fruit} in stock.`);
}


// 4. Advanced: Filtering an object using Object.entries and Object.fromEntries
// Task: Keep only fruits that have a quantity greater than 9
const highStockInventory = Object.fromEntries(
  Object.entries(inventory).filter(([fruit, qty]) => qty > 9)
);

console.log("High Stock:", highStockInventory);
// Output: { apples: 10, bananas: 25, dates: 12 } (cherries are gone!)
```

---

## Related
* [[js-iterable-vs-enumerable]] - Objects are enumerable, not iterable.
* [[js-destructuring]] - Unpacking property tuples during loops.
* [[MOC - JS Objects & Structures]] - Hub for object operations.
