---
id: 202607280813
title: Deep Cloning via structuredClone()
aliases:
  - structuredClone
  - deep clone
  - HTML structured clone algorithm
  - deep copy javascript
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/objects
  - topic/memory
date_created: 2026-07-28
mastery_level: 1
---

# Deep Cloning via `structuredClone()`

> **TL;DR:** Object spread `{ ...obj }` is like photocopying a document containing a map with a star on it—both the photocopy and the original point to the exact same physical location on Earth. `structuredClone()` builds an entire duplicate miniature replica of Earth, so modifying the location on your replica leaves the original planet completely untouched!

---

## Why This Exists
JavaScript developers frequently need to duplicate objects without leaving shared reference linkages. Historically, developers relied on:
1. **Shallow copy utilities** (`{ ...obj }` or `Object.assign({}, obj)`), which leave nested objects and arrays shared by reference.
2. **`JSON.parse(JSON.stringify(obj))` hacks**, which drop `Date` objects (converting them to ISO strings), drop `RegExp`, `Map`, `Set`, `TypedArray`, throw exceptions on circular references, and omit `undefined`, functions, and Symbols.
3. **Heavy external libraries** like Lodash (`_.cloneDeep`).

Introduced in modern environments (Node.js 17+, modern browsers), `structuredClone()` provides a native, high-performance algorithm for true deep cloning that handles complex built-in types and circular references safely.

---

## Mental Model
Imagine a complex tree structure with nested branches, nodes, leaves, Dates, Maps, and cyclic loops (a branch bending back to touch the trunk).
- Shallow Spread `{ ...tree }`: Cuts off the top branches and pastes them onto a new trunk, but the smaller twigs and leaves are still physically attached to the original tree.
- `structuredClone(tree)`: Scans every branch, twig, leaf, date tag, and loop using a recursive graph walker algorithm, recreates every single node independently in new heap memory, and wires all internal connections back together perfectly.

```
ORIGINAL OBJECT (0x100)                     STRUCTURED CLONE (0x900)
┌───────────────────────────┐               ┌───────────────────────────┐
│ profile: 0x200 ───────────┼──┐            │ profile: 0x950 ───────────┼──┐
└───────────────────────────┘  │            └───────────────────────────┘  │
                               │                                           │
                               ▼                                           ▼
                    ┌─────────────────────┐                     ┌─────────────────────┐
                    │ city: "Hyderabad"   │                     │ city: "Hyderabad"   │
                    │ date: Date Object   │                     │ date: Date Object   │
                    └─────────────────────┘                     └─────────────────────┘
                    (Independent Heap 0x200)                    (Independent Heap 0x950)
```

---

## How It Works

1. **HTML Structured Clone Algorithm:** `structuredClone(value, [options])` uses the HTML standard structured clone graph serialization algorithm.
2. **Graph Traversal:** It walks the entire object graph recursively:
   - Duplicates top-level and all nested objects/arrays.
   - Preserves complex types: `Date`, `RegExp`, `Map`, `Set`, `ArrayBuffer`, `TypedArray`, `Blob`, `File`, `Error`.
   - Handles **Circular References** seamlessly by tracking identity maps during traversal.
3. **Unsupported Types Failure:** If the graph encounters a type that cannot be serialized (e.g., Functions, DOM nodes, Proxy objects, WeakMap/WeakSet), it throws a `DataCloneError` DOMException.

---

## Key Characteristics
- **Native Performance:** Implemented in C++ at the JS engine/runtime level, significantly faster than JS-based recursive cloning libraries.
- **Circular Reference Support:** Clones self-referential structures without falling into infinite recursion stack overflows.
- **Transferable Objects Support:** Allows transferring ownership of heavy binary buffers (e.g., `ArrayBuffer`) via `{ transfer: [buffer] }` for zero-copy thread messaging.

---

## Common Mistakes

### Mistake 1: Attempting to clone objects containing functions or methods
`structuredClone()` throws an explicit error when encountering executable code.

```javascript
const user = {
  name: "Luffy",
  greet() { console.log("Hi"); } // Function!
};

// CRASH! DataCloneError: greet could not be cloned.
const copy = structuredClone(user); 
```

### Mistake 2: Expecting prototype inheritance methods to survive
`structuredClone()` clones raw data structures, **not** custom class prototype chains or internal descriptors (`getters`/`setters`/`writable` flags)!

```javascript
class User { greet() { return "Hi"; } }
const instance = new User();

const clone = structuredClone(instance);
console.log(clone instanceof User); // Output: false! (Prototype link lost!)
// clone.greet(); // TypeError: clone.greet is not a function
```

---

## Best Practices
- **Use `structuredClone()` as the default choice** for deep cloning pure data objects, API payload templates, state trees, Maps, Sets, and Dates.
- **Use spread syntax `{ ...obj }`** for flat, single-level objects where deep cloning overhead is unnecessary.
- **Avoid `JSON.parse(JSON.stringify(obj))`** in modern JavaScript codebases.

---

## Comparison Table

| Feature / Capability | `{ ...obj }` (Spread) | `JSON.parse(JSON.stringify())` | `structuredClone()` |
| :--- | :--- | :--- | :--- |
| **Cloning Scope** | Shallow (1st Level) | Deep (Limited Types) | **True Deep Copy** |
| **Circular References** | Shared | **Throws TypeError** | **Fully Supported** |
| **`Date` Objects Support**| Shared | Converted to ISO String | **Preserved as `Date`**|
| **`Map` & `Set` Support** | Shared | Serialized to `{}` (Data Lost) | **Preserved natively** |
| **Functions Support** | Shared Reference | Omitted silently | **Throws DataCloneError**|
| **Prototype Chain Link** | Preserves top prototype | Lost (`Object.prototype`) | Lost (`Object.prototype`)|

---

## Canonical Code Example

```javascript
/**
 * Demonstrates structuredClone() handling nested objects, Dates, Maps,
 * circular references, and error handling for functions.
 */

// 1. Complex Source Data with Built-in Types & Circular References
const originalState = {
  user: {
    id: 101,
    name: "Monkey D. Luffy",
    joinedDate: new Date("2024-01-01T00:00:00.000Z"),
    preferences: new Map([["theme", "dark"], ["notifications", true]]),
    tags: new Set(["captain", "pirate", "emperor"])
  },
  metadata: { version: 1 }
};

// Create a Circular Reference
originalState.user.selfReference = originalState.user;

// 2. Performing Native Deep Clone
const clonedState = structuredClone(originalState);

// 3. Verifying Independence & Deep Copy Integrity
clonedState.user.name = "Roronoa Zoro";
clonedState.user.preferences.set("theme", "light");
clonedState.user.tags.add("swordsman");

console.log("Original Name:", originalState.user.name); // Expected: "Monkey D. Luffy" (Unchanged!)
console.log("Cloned Name:", clonedState.user.name);     // Expected: "Roronoa Zoro"

console.log("Original Theme:", originalState.user.preferences.get("theme")); // Expected: "dark"
console.log("Cloned Theme:", clonedState.user.preferences.get("theme"));   // Expected: "light"

// 4. Verifying Preservation of Date Instances (Unlike JSON.stringify)
console.log("Is Date Instance:", clonedState.user.joinedDate instanceof Date); // Expected: true
console.log("ISO String:", clonedState.user.joinedDate.toISOString()); // Expected: "2024-01-01T00:00:00.000Z"

// 5. Verifying Circular Reference Handling
console.log("Circular Reference Preserved:", clonedState.user.selfReference === clonedState.user); 
// Expected: true
console.log("Circular Unlinked from Original:", clonedState.user.selfReference !== originalState.user); 
// Expected: true

// 6. Demonstrating DataCloneError Hazard with Functions
const objectWithFunction = {
  data: 123,
  action: function() { return "test"; }
};

try {
  structuredClone(objectWithFunction);
} catch (err) {
  console.log("Caught Expected Error:", err.name); // Expected: "DataCloneError" (or DOMException)
}
```

---

## Key Takeaways
- `structuredClone()` is the native JavaScript standard API for deep cloning complex objects.
- It safely handles nested structures, `Date`, `RegExp`, `Map`, `Set`, `ArrayBuffer`, and **Circular References**.
- It throws a `DataCloneError` if it encounters non-serializable values like functions, DOM elements, or Proxy wrappers.
- It does not preserve custom class prototype chains (`instanceof ClassName` evaluates to `false`).

---

## Related
- [[js-object-assign-vs-spread]] — Learn shallow cloning mechanics and performance tradeoffs.
- [[js-json-serialization-parsing]] — Comparing native deep clone vs JSON stringify/parse hacks.
- [[js-weakmap-garbage-collection]] — Why WeakMaps cannot be cloned by `structuredClone()`.
- [[MOC - JS Objects & Structures]] — Central Map of Content for Objects.
