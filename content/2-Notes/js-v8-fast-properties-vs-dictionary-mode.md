---
id: 202607280817
title: Fast Properties vs Dictionary Mode in V8
aliases:
  - fast properties
  - dictionary mode
  - slow properties
  - in-object properties
  - V8 property storage
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/v8-internals
  - topic/performance
date_created: 2026-07-28
mastery_level: 1
---

# Fast Properties vs Dictionary Mode in V8

> **TL;DR:** Fast properties store object data like books neatly arranged on fixed library shelves (direct contiguous memory offsets). Dictionary mode happens when an earthquake messes up the library, forcing the librarian to stash everything in a giant alphabetical card index drawer (a hash table). Reading from a shelf is instantaneous, while digging through card index drawers is slow!

---

## Why This Exists
V8's ultimate goal is to make JavaScript property access as fast as C++ struct field access. To achieve this, V8 uses **Fast Properties** stored in contiguous array slots. 

However, JavaScript objects are dynamic. If an application constantly deletes properties or dynamically attaches dozens of out-of-order keys to an object, maintaining complex Hidden Class transition trees and inline caches becomes more expensive than the optimization itself. In response, V8 switches the object's internal storage strategy from Fast Properties to **Dictionary Mode** (Slow Properties), sacrificing raw speed to preserve memory and flexibility.

---

## Mental Model
Think of V8 property storage as a two-tier storage system:
- **Fast Properties (In-Object & Out-of-Object Arrays):** Properties sit in fixed sequential memory slots directly inside the object header (In-Object) or in a contiguous backup array. A Hidden Class specifies exact index offsets (`slot 0`, `slot 1`).
- **Dictionary Mode (Normalized Hash Table):** V8 dismantles the Hidden Class link completely. The object converts into a self-contained Hash Table dictionary where keys, values, and descriptor flags are looked up dynamically by string hash on every access.

```
FAST PROPERTIES LAYOUT (Shape C1)
┌───────────────────────────────────────────────┐
│ Object Header ──► Points to Shape C1          │
├───────────────────────────────────────────────┤
│ Slot 0: "Luffy" (In-Object Property: name)    │
│ Slot 1: 19      (In-Object Property: age)     │
└───────────────────────────────────────────────┘

SLOW DICTIONARY MODE LAYOUT (No Shape)
┌───────────────────────────────────────────────┐
│ Object Header ──► Points to Null/Slow Shape   │
├───────────────────────────────────────────────┤
│ Hash Table Array:                             │
│   [ "name" ──► "Luffy" (writable, enum...) ]  │
│   [ "age"  ──► 19      (writable, enum...) ]  │
└───────────────────────────────────────────────┘
```

---

## How It Works

V8 uses three distinct internal storage mechanisms for object properties:

### 1. In-Object Fast Properties (FASTEST)
- Properties are stored directly inside the object payload in memory.
- Pre-allocated when the object is instantiated based on constructor hints.
- Access requires zero array indirection: pointer offset calculated directly.

### 2. Out-of-Object Fast Properties (FAST)
- If in-object capacity (typically 10 slots) is exceeded, extra properties are placed into a contiguous backing store array.
- Requires 1 pointer dereference to access the backing store array + slot offset.

### 3. Dictionary Mode / Slow Properties (SLOWEST)
- Triggered when:
  - Properties are deleted via the `delete` operator.
  - An excessive number of dynamic properties are added to a single object.
  - Properties with unusual names/numeric keys break standard transition chains.
- V8 detaches the object from its Hidden Class transition tree and assigns a dedicated **NameDictionary** hash table.
- Inline Caches (ICs) deoptimize and bypass direct memory access.

---

## Key Characteristics
- **Automatic Transitions:** V8 automatically transitions objects from Fast to Dictionary mode when structural churn occurs.
- **Reversibility:** Objects rarely transition back from Dictionary Mode to Fast Properties once demoted.
- **`delete` Hazard:** The `delete` operator is the single most common trigger for Dictionary Mode demotion.

---

## Common Mistakes

### Mistake 1: Using `delete` in performance-critical hot loops
Deleting properties changes the internal structure, invalidates hidden classes, and pushes objects into dictionary mode.

```javascript
const user = { name: "Luffy", age: 19 };

// BAD: Triggers V8 Dictionary Mode demotion!
delete user.age; 

// GOOD: Assign undefined or null to retain Fast Properties layout:
user.age = undefined;
```

### Mistake 2: Dynamically appending hundreds of un-preset properties to an object
Treating plain objects like infinite dynamic hash maps eventually forces V8 to convert the object to Dictionary Mode.

---

## Best Practices
- **Use `Map` for dynamic key-value collections** with frequent additions and deletions instead of plain objects.
- **Assign `undefined` or `null` instead of `delete`** when clearing property values in performance-critical code.
- **Declare all anticipated properties in constructors or object literals** to maximize In-Object Fast Property allocation.

---

## Comparison Table

| Attribute / Feature | In-Object Fast Properties | Out-of-Object Fast Properties | Dictionary Mode (Slow) |
| :--- | :--- | :--- | :--- |
| **Storage Location** | Directly inside Object Header | Extra Backing Array Pointer | Dedicated NameDictionary Hash Table |
| **Memory Pointer Overhead**| **0 Pointer Indirections** | 1 Pointer Indirection | Full Hash Lookup Traversal |
| **Lookup Speed** | **Fastest (~1 CPU cycle)** | Fast (~2 CPU cycles) | **Slowest (`O(1)` Hash Overhead)** |
| **V8 Hidden Class Link**| Active Shared Shape | Active Shared Shape | **Dismantled / Bypassed** |
| **Triggered By** | Preset Object Literals/Classes | Exceeding In-Object capacity | `delete` operator & mass dynamic additions |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates Fast Properties vs Dictionary Mode performance behavior,
 * highlighting the performance penalty of the delete operator in V8.
 */

// 1. Helper to create Fast Property Objects
function createFastObject(i) {
  return {
    id: i,
    name: `User_${i}`,
    role: "Developer",
    status: "Active"
  };
}

// 2. Helper to create Dictionary Mode Objects (demoted via delete)
function createDictionaryObject(i) {
  const obj = {
    id: i,
    name: `User_${i}`,
    role: "Developer",
    status: "Active",
    tempField: true
  };
  
  // Forces V8 to transition obj to Dictionary Mode / Slow Properties!
  delete obj.tempField; 
  return obj;
}

const COUNT = 1_000_000;
const fastArray = new Array(COUNT);
const dictArray = new Array(COUNT);

for (let i = 0; i < COUNT; i++) {
  fastArray[i] = createFastObject(i);
  dictArray[i] = createDictionaryObject(i);
}

// 3. Benchmarking Property Access on Fast Properties
console.time("Fast Properties Read Loop");
let sum1 = 0;
for (let i = 0; i < COUNT; i++) {
  sum1 += fastArray[i].id + fastArray[i].status.length;
}
console.timeEnd("Fast Properties Read Loop");

// 4. Benchmarking Property Access on Dictionary Mode Objects
console.time("Dictionary Mode Read Loop");
let sum2 = 0;
for (let i = 0; i < COUNT; i++) {
  sum2 += dictArray[i].id + dictArray[i].status.length;
}
console.timeEnd("Dictionary Mode Read Loop");

console.log("Integrity Check Sums:", sum1 === sum2); // Expected: true
```

---

## Key Takeaways
- Fast Properties store data at contiguous memory offsets using V8 Hidden Classes for `O(1)` access.
- In-Object properties reside directly inside the object payload in memory, offering maximum performance.
- Using `delete` or adding excessive out-of-order keys demotes objects to **Dictionary Mode** (slow hash table storage).
- Use `Map` when building dynamic collections with frequent additions/deletions, reserving objects for fixed-shape structures.

---

## Related
- [[js-v8-hidden-classes-shapes]] — Understanding V8 shapes and transition chains.
- [[js-v8-inline-caches]] — How Inline Caching relies on Fast Properties.
- [[js-map-vs-object]] — Detailed architectural guide on selecting `Map` vs `Object`.
- [[MOC - JS Advanced Mechanics & Metaprogramming]] — Central Map of Content for Engine Internals.
