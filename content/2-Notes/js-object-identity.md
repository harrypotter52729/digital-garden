---
id: 202607280807
title: Object Identity & Reference Equality
aliases:
  - object identity
  - reference equality
  - deep equality vs shallow equality
  - object comparison
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/objects
  - topic/comparison
date_created: 2026-07-28
mastery_level: 1
---

# Object Identity & Reference Equality

> **TL;DR:** Two newly minted identical twin cars manufactured back-to-back are separate objects with different VIN numbers. Even if every part is identical, `carA === carB` returns `false` because JavaScript compares their physical identity (the VIN number/memory address), not their visual appearance!

---

## Why This Exists
In JavaScript, primitive types are compared by value (**Structural Equality**), meaning `5 === 5` evaluates to `true` because the underlying raw values are identical. However, objects possess **Identity**. 

Because objects can contain complex, dynamic structures and cyclic references, evaluating deep structural equality on every `===` comparison would incur an `O(N)` performance penalty. Therefore, JavaScript enforces strict **Reference Equality** (`O(1)` pointer address comparison) for all object comparisons.

---

## Mental Model
Think of two identical modern smartphones fresh out of the box with the exact same specs, wallpaper, and files. Even though their content is 100% identical, they exist at different physical points in space (distinct serial numbers / memory locations). Checking `phoneA === phoneB` asks: *"Are these two variables pointing to the exact same physical phone?"* Not: *"Do these phones have the same content?"*

```
Case 1: Two separate object literals {} === {}
Variable A ──► [ Pointer 0x1000 ] ──► Heap: { x: 1 }
Variable B ──► [ Pointer 0x2000 ] ──► Heap: { x: 1 }
Result: 0x1000 === 0x2000 ──► FALSE

Case 2: Shared reference assignment (const B = A)
Variable A ──► [ Pointer 0x1000 ] ──┐
                                    ├──► Heap: { x: 1 }
Variable B ──► [ Pointer 0x1000 ] ──┘
Result: 0x1000 === 0x1000 ──► TRUE
```

---

## How It Works

1. **Equality Operators Behavior:** Both loose equality (`==`) and strict equality (`===`) behave identically when comparing objects: they evaluate whether both operands reference the exact same memory address pointer.
2. **Literal Evaluation:** Every time an object literal `{}` or array literal `[]` is evaluated in code, the JS runtime allocates a brand new memory slot with a unique memory reference pointer.
3. **`Object.is()` Comparison:** `Object.is(objA, objB)` also uses reference equality for objects, producing identical results to `===` when comparing objects.

---

## Key Characteristics
- **`{} === {}` is ALWAYS `false`:** Each literal evaluation instantiates a separate object with a unique identity.
- **Reference Persistence:** Object identity remains intact regardless of property additions, deletions, or updates.
- **No Native Structural Equality:** JavaScript has no built-in operator for comparing object content deeply. Deep comparison requires manual recursion, `JSON.stringify()` (with caveats), or libraries like Lodash (`_.isEqual`).

---

## Common Mistakes

### Mistake 1: Using `===` or `==` to test if two objects have the same data
Developers expect two objects with identical properties to evaluate as equal.

```javascript
const userA = { id: 101, name: "Luffy" };
const userB = { id: 101, name: "Luffy" };

console.log(userA === userB); // Output: false! (Different memory addresses)
```

### Mistake 2: Relying on `JSON.stringify()` for structural equality without considering key ordering
Using `JSON.stringify(a) === JSON.stringify(b)` fails if keys were inserted in different orders!

```javascript
const a = { x: 1, y: 2 };
const b = { y: 2, x: 1 };

console.log(JSON.stringify(a) === JSON.stringify(b)); // Output: false! (String key orders differ)
```

---

## Best Practices
- **Use `===` when checking for instance identity** (e.g., verifying if an object in state is the exact same object reference).
- **Implement custom deep equality functions** or use verified utilities (`lodash.isEqual`) when testing business data equality across API payloads or state snapshots.
- **Beware of object keys order and non-serializable fields** (functions, `undefined`, Symbols) when attempting stringification comparisons.

---

## Comparison Table

| Comparison Method | Checks What? | `{} === {}` | `{a:1} vs {a:1}` | Key Order Dependent? | Handles Functions/Symbols? |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Strict Equality (`===`)** | Pointer Address | `false` | `false` | N/A | Yes (by pointer) |
| **`Object.is()`** | Pointer Address | `false` | `false` | N/A | Yes (by pointer) |
| **`JSON.stringify()`** | Serialized String | `true` | `true` | **YES (Fails if mixed)** | **NO (Omitted/Nullified)** |
| **Recursive Deep Equal** | Structural Data Values | `true` | `true` | No | Yes (via custom rules) |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates reference equality vs structural comparison algorithms,
 * detailing identity traps and key ordering edge cases.
 */

// 1. Distinct Object Instantiation
const objA = { title: "JS Masterclass", chapters: [1, 2, 3] };
const objB = { title: "JS Masterclass", chapters: [1, 2, 3] };

// Identity Comparison (Reference Checks)
console.log("objA === objB:", objA === objB); // Expected: false
console.log("Object.is(objA, objB):", Object.is(objA, objB)); // Expected: false

// 2. Shared Identity
const objC = objA;
console.log("objA === objC:", objA === objC); // Expected: true

// 3. Simple Deep Equality Utility Algorithm
function isDeepEqual(obj1, obj2) {
  if (obj1 === obj2) return true;

  if (
    typeof obj1 !== "object" || obj1 === null ||
    typeof obj2 !== "object" || obj2 === null
  ) {
    return false;
  }

  const keys1 = Reflect.ownKeys(obj1);
  const keys2 = Reflect.ownKeys(obj2);

  if (keys1.length !== keys2.length) return false;

  for (const key of keys1) {
    if (!keys2.includes(key) || !isDeepEqual(obj1[key], obj2[key])) {
      return false;
    }
  }

  return true;
}

// 4. Testing Structural Equality
console.log("Deep Equal (objA, objB):", isDeepEqual(objA, objB)); // Expected: true

// 5. Key Ordering Edge Case with JSON.stringify
const unorderedA = { a: 100, b: 200 };
const unorderedB = { b: 200, a: 100 };

console.log("JSON Stringify Match:", JSON.stringify(unorderedA) === JSON.stringify(unorderedB)); 
// Expected Output: false (Stringify is key-order sensitive!)

console.log("Deep Equal Key Order Check:", isDeepEqual(unorderedA, unorderedB)); 
// Expected Output: true (Recursion accurately checks keys regardless of order!)
```

---

## Key Takeaways
- Object comparison in JavaScript (`==`, `===`, `Object.is()`) checks reference pointer equality, never structural contents.
- Two distinct object literals `{}` will never be equal to each other because they reside at separate heap addresses.
- `JSON.stringify()` is a fragile workaround for structural comparison because it is order-dependent and drops non-JSON types.
- To compare object contents accurately, use a recursive deep-equality algorithm that inspects own keys and nested properties.

---

## Related
- [[js-objects-reference-types]] — Foundations of memory pointers and stack storage.
- [[js-object-property-existence-checks]] — How to check property keys accurately.
- [[js-json-serialization-parsing]] — Full breakdown of JSON serialization behavior and traps.
- [[MOC - JS Objects & Structures]] — Central Map of Content for JavaScript Objects.
