---
id: 202607280838
title: Native Collection Grouping via Object.groupBy() & Map.groupBy()
aliases:
  - Object.groupBy
  - Map.groupBy
  - array grouping
  - ES2024 groupBy
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/objects
  - topic/es2024
date_created: 2026-07-28
mastery_level: 1
---

# Native Collection Grouping via `Object.groupBy()` & `Map.groupBy()`

> **TL;DR:** Imagine a mail sorting room sorting letters into postal code bins. Before ES2024, developers had to write complex, error-prone `array.reduce()` accumulator boilerplate code to bucket items. `Object.groupBy()` and `Map.groupBy()` provide native, high-performance automated mail sorters that bucket items into groups based on your callback function!

---

## Why This Exists
Grouping elements of an array or iterable by a specific property (e.g. grouping a list of products by category, or users by role) is one of the most common data transformation tasks in application development, backend services, and test automation reporting.

Before ES2024, JavaScript lacked native grouping utilities. Developers relied on manual `Array.prototype.reduce()` implementations or external libraries like Lodash (`_.groupBy`). ES2024 introduced **`Object.groupBy()`** and **`Map.groupBy()`** as native static utility methods for clean, standardized data grouping.

---

## Mental Model
Think of grouping as sorting items into labeled buckets:
- **`Object.groupBy(iterable, callback)`**: Sorts items into buckets labeled by **String property keys**. Returns a plain object `{ categoryA: [...], categoryB: [...] }`.
- **`Map.groupBy(iterable, callback)`**: Sorts items into buckets labeled by **Arbitrary key types** (objects, numbers, booleans, functions). Returns a `Map` instance `Map(key => [...])`.

```
INPUT ARRAY: [ { name: "Luffy", role: "Pirate" }, { name: "Coby", role: "Marine" } ]
                        │
                        ▼ Callback: item => item.role
Object.groupBy() ──► Plain Object: { Pirate: [...], Marine: [...] }
Map.groupBy()    ──► Map Instance: Map( "Pirate" => [...], "Marine" => [...] )
```

---

## How It Works

1. **`Object.groupBy(items, callback)`**:
   - Iterates through `items` (any iterable, e.g. Array, Set).
   - Invokes `callback(element, index)` for each item.
   - Coerces the callback return value to a **String** (or Symbol).
   - Groups matching elements into arrays attached to keys on a fresh null-prototype or plain object.
2. **`Map.groupBy(items, callback)`**:
   - Iterates through `items`.
   - Invokes `callback(element, index)`.
   - Preserves the **exact returned key type** (Objects, Numbers, Booleans, etc.) without string coercion using SameValueZero equality.
   - Groups matching elements into arrays stored inside a new `Map` instance.

---

## Key Characteristics
- **Null / Undefined Handling:** If the callback returns `null` or `undefined`, `Object.groupBy()` coerces them to string keys `"null"` and `"undefined"`, whereas `Map.groupBy()` keeps `null` and `undefined` as exact key references.
- **Pure & Non-Mutating:** Neither method mutates the source array/iterable; both return fresh collections.

---

## Common Mistakes

### Mistake 1: Using `Object.groupBy()` when grouping by object or non-string keys
`Object.groupBy()` coerces non-string keys into strings, turning object keys into `"[object Object]"`.

```javascript
const categoryObj = { id: 1 };
const items = [{ name: "Item 1", cat: categoryObj }];

// BAD: Object.groupBy coerces categoryObj to "[object Object]"!
const result = Object.groupBy(items, item => item.cat);
console.log(result["[object Object]"]); // Output: [{ name: "Item 1", cat: ... }]

// GOOD: Use Map.groupBy to preserve object key references:
const mapResult = Map.groupBy(items, item => item.cat);
console.log(mapResult.get(categoryObj)); // Output: [{ name: "Item 1", cat: ... }]
```

---

## Best Practices
- **Use `Object.groupBy()`** when keys are natural strings (e.g. status codes, roles, categories) and JSON compatibility is needed.
- **Use `Map.groupBy()`** when keys are objects, numbers, booleans, or when maintaining exact key type identity is required.
- **Replace custom `array.reduce()` grouping hacks** with native `groupBy()` in modern codebases (Node 21+, modern browsers).

---

## Comparison Table

| Attribute | `Object.groupBy()` | `Map.groupBy()` | Legacy `reduce()` |
| :--- | :--- | :--- | :--- |
| **Return Type** | Plain Object `{}` | `Map` Instance | Custom (Developer defined) |
| **Key Coercion** | Auto-coerced to String | **No Coercion (Preserves Type)** | Manual |
| **JSON Support** | **Native (`JSON.stringify`)** | Requires conversion | Varies |
| **Key Equality** | String Comparison | SameValueZero | Varies |
| **Performance** | Native Engine C++ Speed | Native Engine C++ Speed | JS Execution Loop |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates native ES2024 Object.groupBy() and Map.groupBy(),
 * key coercion differences, and practical application scenarios.
 */

// 1. Source Data Array
const inventory = [
  { name: "Laptop", category: "Electronics", price: 1200, inStock: true },
  { name: "Mouse", category: "Electronics", price: 25, inStock: true },
  { name: "Shirt", category: "Apparel", price: 45, inStock: false },
  { name: "Pants", category: "Apparel", price: 60, inStock: true }
];

// 2. Object.groupBy Demonstration (String Keys)
const groupedByCategory = Object.groupBy(inventory, item => item.category);

console.log("Electronics Group:", groupedByCategory.Electronics.map(i => i.name)); 
// Expected: ["Laptop", "Mouse"]
console.log("Apparel Group:", groupedByCategory.Apparel.map(i => i.name)); 
// Expected: ["Shirt", "Pants"]

// 3. Dynamic Condition Grouping with Object.groupBy
const stockStatus = Object.groupBy(inventory, item => item.inStock ? "available" : "outOfStock");
console.log("Available Count:", stockStatus.available.length); // Expected: 3
console.log("Out of Stock Count:", stockStatus.outOfStock.length); // Expected: 1

// 4. Map.groupBy Demonstration (Non-String Keys / Complex Objects)
const lowPriceTier = { tier: "Budget", max: 50 };
const highPriceTier = { tier: "Premium", max: 5000 };

const groupedByTierObject = Map.groupBy(inventory, item => {
  return item.price <= 50 ? lowPriceTier : highPriceTier;
});

// Retrieving groups using exact object key references!
console.log("Budget Items Count:", groupedByTierObject.get(lowPriceTier).length); // Expected: 2 (Mouse, Shirt)
console.log("Premium Items Count:", groupedByTierObject.get(highPriceTier).length); // Expected: 2 (Laptop, Pants)
```

---

## Key Takeaways
- ES2024 introduced `Object.groupBy()` and `Map.groupBy()` to standardize collection grouping natively.
- `Object.groupBy()` returns a plain object with string keys, ideal for JSON-friendly data.
- `Map.groupBy()` returns a `Map` instance preserving exact key types (objects, numbers, booleans).
- Both methods replace verbose, error-prone `Array.prototype.reduce()` grouping boilerplates.

---

## Related
- [[js-object-keys-values-entries]] — Object key-value inspection methods.
- [[js-map-vs-object]] — Selecting between Map and Object structures.
- [[js-json-serialization-parsing]] — Serializing grouped objects to JSON.
- [[MOC - JS Objects & Structures]] — Central Map of Content for Objects.
