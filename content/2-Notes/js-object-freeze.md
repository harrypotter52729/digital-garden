---
id: 202607280812
title: Object Protection via Object.freeze() & Deep Freeze
aliases:
  - Object.freeze
  - shallow freeze
  - deep freeze
  - frozen objects
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/immutability
  - topic/security
date_created: 2026-07-28
mastery_level: 1
---

# Object Protection via `Object.freeze()` & Deep Freeze

> **TL;DR:** Calling `Object.freeze(obj)` is like encasing a glass display case in solid clear ice. You can look through the ice at top-level items, but nobody can add new items, delete items, or swap out top-level items. However, if a top-level item is a sealed plastic box (a nested object), items *inside* that nested box can still be changed unless you freeze every nested box recursively!

---

## Why This Exists
By default, JavaScript objects are completely mutable. Any module or developer with a reference to an object can mutate properties, delete keys, or attach new fields. In application configuration, shared state management (React/Redux), and security contexts, accidental mutations create subtle bugs that are difficult to track.

`Object.freeze()` provides the highest level of built-in language protection for plain objects, locking down top-level data structures to prevent addition, deletion, or modification of properties.

---

## Mental Model
Imagine a administrative file folder:
- Unprotected Object: Anyone can write on papers, add new papers, or throw papers in the trash.
- `Object.preventExtensions()`: Locks the binder rings (cannot add new pages), but existing pages can be edited or deleted.
- `Object.seal()`: Locks the binder rings AND glues pages to the binder (cannot add or delete pages), but you can still erase and rewrite text on existing pages.
- `Object.freeze()`: Locks binder rings, glues pages, AND lamination covers every page in plastic (cannot add, delete, or rewrite top-level text).

```
Object Mutation Protections Comparison Matrix
┌───────────────────────┬──────────────┬──────────────┬──────────────┐
│ Protection Level      │ Add Keys?    │ Delete Keys? │ Modify Data? │
├───────────────────────┼──────────────┼──────────────┼──────────────┤
│ preventExtensions()   │ ❌ NO        │ ✅ YES       │ ✅ YES       │
│ seal()                │ ❌ NO        │ ❌ NO        │ ✅ YES       │
│ freeze()              │ ❌ NO        │ ❌ NO        │ ❌ NO        │
└───────────────────────┴──────────────┴──────────────┴──────────────┘
```

---

## How It Works

1. **Top-Level Protection Mechanics:** Calling `Object.freeze(obj)` performs three automated actions under the hood:
   - Sets the internal ```[[js-ecmascript-internal-slots|[[Extensible]]]]``` slot of `obj` to `false` (blocking new property additions).
   - Sets `configurable: false` on all existing own property descriptors (blocking deletion or flag changes).
   - Sets `writable: false` on all existing data property descriptors (blocking value mutations).
2. **Shallow Immutability Limitation:** `Object.freeze()` is **shallow**. If an object property references a nested object, array, or function, the nested object's internal properties remain completely mutable!
3. **Verification:** `Object.isFrozen(obj)` returns `true` if an object is frozen.

---

## Key Characteristics
- **Strict Mode Sensitivity:** Attempting to mutate, add, or delete properties on a frozen object fails silently in non-strict mode, but throws a runtime `TypeError` in strict mode (`"use strict"`).
- **Prototype Chain Immunity:** Freezing an instance does not freeze its prototype (`Object.getPrototypeOf(obj)`).
- **Engine Optimizations:** V8 optimizes access to frozen objects because property shapes and values are guaranteed stable.

---

## Common Mistakes

### Mistake 1: Assuming `Object.freeze()` performs a deep freeze
Developers assume freezing a complex nested config object protects nested structures.

```javascript
const config = Object.freeze({
  env: "production",
  db: { host: "localhost" } // Nested object!
});

config.env = "staging"; // Mutation blocked!
config.db.host = "10.0.0.1"; // MUTATES! Nested objects remain mutable!

console.log(config.db.host); // Output: "10.0.0.1"
```

### Mistake 2: Expecting `Object.freeze()` to lock down arrays referenced inside objects
Arrays are objects in JavaScript. Freezing an object containing an array does not prevent `.push()`, `.pop()`, or index assignment on that array!

---

## Best Practices
- **Use `Object.freeze()` on static configuration objects** and constant lookup tables to enforce immutability across teams.
- **Implement a recursive `deepFreeze()` helper** when freezing deeply nested data structures or state snapshots.
- **Enforce Strict Mode (`"use strict"`)** across codebases so illegal mutations on frozen objects throw explicit errors instead of failing silently.

---

## Comparison Table

| Feature / Behavior | Plain Object `{}` | `preventExtensions()` | `Object.seal()` | `Object.freeze()` |
| :--- | :--- | :--- | :--- | :--- |
| **Add New Properties**| ✅ Allowed | ❌ Blocked | ❌ Blocked | ❌ Blocked |
| **Delete Existing Keys**| ✅ Allowed | ✅ Allowed | ❌ Blocked | ❌ Blocked |
| **Modify Values** | ✅ Allowed | ✅ Allowed | ✅ Allowed | ❌ **Blocked** |
| **Reconfigure Flags** | ✅ Allowed | ✅ Allowed | ❌ Blocked | ❌ Blocked |
| **Shallow Scope** | N/A | Yes | Yes | **Yes (Requires Deep Freeze)**|

---

## Canonical Code Example

```javascript
/**
 * Demonstrates Object.freeze() behavior, strict mode failure traps,
 * shallow freeze limitations, and a production-grade deepFreeze recursive algorithm.
 */
"use strict";

// 1. Shallow Freeze Demonstration
const appConfig = Object.freeze({
  appName: "VaultApp",
  version: "2.1.0",
  endpoints: {
    api: "https://api.vault.com",
    auth: "https://auth.vault.com"
  }
});

console.log("Is Frozen:", Object.isFrozen(appConfig)); // Expected: true

// Top-level mutation attempt in strict mode
try {
  appConfig.version = "3.0.0"; 
} catch (err) {
  console.log("Blocked Top-Level Mutation:", err.message);
  // Expected Output: Cannot assign to read only property 'version' of object...
}

// 2. Proving Shallow Freeze Vulnerability
appConfig.endpoints.api = "http://hacked-api.com"; // SUCCESSFUL MUTATION!
console.log("Mutated Shallow Endpoint:", appConfig.endpoints.api); 
// Expected Output: "http://hacked-api.com"

// 3. Production-Grade Deep Freeze Implementation
function deepFreeze(obj) {
  // Retrieve own keys (including non-enumerable & Symbol keys)
  const propNames = Reflect.ownKeys(obj);

  for (const name of propNames) {
    const value = obj[name];

    // Recursively freeze nested objects/arrays before freezing parent
    if (value && (typeof value === "object" || typeof value === "function")) {
      if (!Object.isFrozen(value)) {
        deepFreeze(value);
      }
    }
  }

  return Object.freeze(obj);
}

// 4. Testing Deep Freeze
const secureConfig = deepFreeze({
  server: {
    host: "127.0.0.1",
    ssl: { enabled: true }
  }
});

try {
  secureConfig.server.ssl.enabled = false; // Attempts deep mutation
} catch (err) {
  console.log("Blocked Deep Mutation:", err.message);
  // Expected Output: Cannot assign to read only property 'enabled' of object...
}

console.log("Is Deep Nested Frozen:", Object.isFrozen(secureConfig.server.ssl)); // Expected: true
```

---

## Key Takeaways
- `Object.freeze()` makes top-level properties read-only (`writable: false`), non-deletable (`configurable: false`), and prevents adding new keys.
- `Object.freeze()` operates shallowly; nested child objects and arrays remain mutable unless explicitly frozen.
- `Object.isFrozen(obj)` checks whether an object is frozen.
- Always implement a recursive `deepFreeze()` utility when working with nested configuration data or application state.

---

## Related
- [[js-object-seal]] — Detailed mechanics of `Object.seal()` vs `preventExtensions()`.
- [[js-property-descriptors]] — Understanding descriptor attributes (`writable`, `configurable`).
- [[js-structured-clone]] — Creating deep unlinked copies instead of freezing shared state.
- [[MOC - JS Objects & Structures]] — Central Map of Content for Objects.
