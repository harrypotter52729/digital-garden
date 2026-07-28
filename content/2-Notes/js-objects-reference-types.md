---
id: 202607280806
title: Objects as Reference Types
aliases:
  - reference types
  - object reference
  - pass by reference
  - memory addresses
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/memory
  - topic/objects
date_created: 2026-07-28
mastery_level: 1
---

# Objects as Reference Types

> **TL;DR:** Imagine a variable holding a primitive like storing a house key in your pocket, whereas a variable holding an object is storing a written home address on a piece of paper. Giving someone a copy of the address does not build a second house—it simply gives two people directions to modify the exact same house!

---

## Why This Exists
In computer memory, primitive data types have fixed, small memory footprints (e.g., a 64-bit float) and can be stored directly inside execution stack frames. In contrast, JavaScript objects are dynamically sized collections that grow, shrink, or nest arbitrarily at runtime. 

Storing large dynamic structures directly in stack memory would cause severe memory stack overflows and copy performance bottlenecks. JavaScript resolves this by storing object contents in Heap Memory and keeping only a fixed-size **memory reference pointer** inside variables.

---

## Mental Model
Think of an object as a physical warehouse situated at GPS location `0x7A9B`. When you assign an object to a variable `const warehouse = { stock: 500 }`, the variable `warehouse` does not contain the building; it contains a slip of paper reading `GPS: 0x7A9B`. If you pass `warehouse` to a function, you copy the slip of paper. Any modification made through either slip of paper updates the single shared warehouse at `0x7A9B`.

```
STACK MEMORY                        HEAP MEMORY
┌──────────────┬─────────────┐      ┌─────────────────────────────┐
│ Variable     │ Reference   │      │ Memory Address: 0x7A9B      │
├──────────────┼─────────────┤      ├─────────────────────────────┤
│ user         │ 0x7A9B  ────┼────► │ {                           │
│ admin        │ 0x7A9B  ────┼────► │   name: "Luffy",            │
└──────────────┴─────────────┘ │   role: "Admin"             │
                                    │ }                           │
                                    └─────────────────────────────┘
```

---

## How It Works

1. **Heap Memory Allocation:** When an object literal `{}` or instantiation occurs, the V8/JS engine allocates memory block space in the Heap Memory area.
2. **Reference Pointer Generation:** The engine returns the starting memory pointer address (e.g., `0x00A4F8`).
3. **Stack Storage:** The variable in the current call stack context stores this address pointer `0x00A4F8` as its value.
4. **Pass-by-Value-of-Reference:** When an object variable is assigned to another variable or passed into a function, JavaScript copies the **value of the pointer**, not the object contents.
5. **Mutation Propagation:** Mutating properties via any variable pointer mutates the underlying heap memory, reflecting the change across all reference holders.

---

## Key Characteristics
- **Shared Access:** Multiple variables referencing the same memory address share access to mutations.
- **Reassignment vs Mutation:** Reassigning a reference variable (`user = { name: "Zoro" }`) overwrites its internal stack address pointer to point to a new heap location, severing its relationship with the old object without mutating it.
- **Const Binding Immutability:** Declaring an object with `const` locks the **variable pointer reference** (preventing `obj = newObj`), but does **not** protect the object's interior properties from mutation!

---

## Common Mistakes

### Mistake 1: Assuming `const` makes an object immutable
Developers often think `const` prevents modifying object contents.

```javascript
const config = { port: 8080 };
config.port = 3000; // VALID! The pointer address hasn't changed.

// config = { port: 3000 }; // TypeError: Assignment to constant variable.
```

### Mistake 2: Unintentional side effects in functions (Impure mutations)
Passing an object into a helper function and mutating its properties mutates caller state across the application.

```javascript
function updateRole(userObj) {
  userObj.role = "Admin"; // Mutates external state unexpectedly!
}
```

---

## Best Practices
- **Treat function arguments as read-only** when working with object references. Return fresh objects using spread operators (`{ ...obj }`) instead of mutating parameters.
- **Use `Object.freeze()`** if you need shallow immutability for reference values.
- **Understand assignment boundaries:** Remember that `= ` reassigns pointers, while `.property = ` mutates heap data.

---

## Comparison Table

| Behavior | Primitives (Value Types) | Objects (Reference Types) |
| :--- | :--- | :--- |
| **Storage Location** | Stack Memory | Heap Memory (Pointer on Stack) |
| **Variable Contents** | Actual raw value (`25`, `"hello"`) | Address Pointer (`0x8F4A`) |
| **Assignment (`a = b`)** | Copies raw value independently | Copies address pointer (Shared reference) |
| **Equality Check (`===`)**| Compares actual raw values | Compares memory addresses |
| **Function Argument Behavior**| Pass-by-value (Safe from side effects)| Pass-by-value-of-reference (Can cause side effects)|

---

## Canonical Code Example

```javascript
/**
 * Demonstrates reference semantics, shared mutation side effects,
 * pointer reassignment, and function parameter interactions.
 */

// 1. Initial Heap Allocation (e.g. at Heap Address 0x100)
const originalUser = {
  id: 101,
  profile: { name: "Luffy", rank: "Captain" }
};

// 2. Copying the reference pointer (shared reference to 0x100)
const aliasedUser = originalUser;

// 3. Mutating heap data via aliased reference
aliasedUser.profile.rank = "Emperor";

console.log("Original User Rank:", originalUser.profile.rank); 
// Expected Output: "Emperor" (Original updated due to shared pointer!)

// 4. Function side-effect demonstration
function impureStatusUpdate(user) {
  // Mutates property directly on reference pointer 0x100
  user.active = true; 
}

impureStatusUpdate(originalUser);
console.log("Original Active State:", originalUser.active); // Expected: true

// 5. Reassignment vs Mutation
function safeReassignment(user) {
  // Overwrites LOCAL stack variable 'user' with a fresh pointer (0x200)
  user = { id: 999, profile: { name: "Zoro", rank: "Swordsman" } };
  console.log("Inside Safe Function:", user.profile.name); // Expected: "Zoro"
}

safeReassignment(originalUser);
console.log("Original Name After Reassignment:", originalUser.profile.name); 
// Expected Output: "Luffy" (Original reference pointer remains untouched!)
```

---

## Key Takeaways
- Variables assigned to objects store memory pointers to heap addresses, not the actual object content.
- Assigning one object variable to another duplicates the pointer, linking both variables to the same physical memory space.
- `const` enforces pointer immutability (you cannot reassign the variable), but object properties remain completely mutable.
- Reassigning an object variable changes what memory address the variable points to; mutating an object changes the data stored at that memory address.

---

## Related
- [[js-object-identity]] — Explains equality checks and identity logic between reference types.
- [[js-stack-vs-heap-memory]] — Detailed engine-level look at stack frames vs heap layouts.
- [[js-object-assign-vs-spread]] — Learn how to create shallow decoupled reference clones.
- [[MOC - JS Data Types & Memory]] — Central Map of Content for JS Memory Architecture.
