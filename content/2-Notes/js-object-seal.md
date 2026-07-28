---
id: 202607280836
title: Object Protection via Object.seal()
aliases:
  - Object.seal
  - sealed objects
  - sealing objects
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/immutability
  - topic/security
date_created: 2026-07-28
mastery_level: 1
---

# Object Protection via `Object.seal()`

> **TL;DR:** Calling `Object.seal(obj)` is like gluing every paper page inside a binder to its ring mechanism. Nobody can tear out existing pages (cannot delete properties) and nobody can add new blank pages (cannot add new properties), but anyone can still edit and rewrite text on the existing pages (updating property values is allowed)!

---

## Why This Exists
JavaScript objects are dynamic by default, allowing property addition, deletion, and value modification. While `Object.freeze()` locks down objects completely (preventing value changes), developers often need a middle ground for structured records: keeping an object's **shape fixed** (preventing property addition or removal) while still allowing **values to be updated**.

`Object.seal()` seals an object to lock its layout shape, preventing property additions and deletions while allowing existing writable properties to accept new values.

---

## Mental Model
Think of `Object.seal()` as locking an administrative form template:
- **`preventExtensions()`**: Prevents printing new form fields. Existing fields can be erased, renamed, or deleted.
- **`seal()`**: Prevents printing new fields AND locks field names in place. Existing fields cannot be deleted, but you can fill in or change the written answers in existing fields.
- **`freeze()`**: Lamination cover over the entire form. Fields cannot be added, deleted, or updated.

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

1. **Sealing Execution Steps:** When `Object.seal(obj)` is called:
   - Sets the internal ```[[js-ecmascript-internal-slots|[[Extensible]]]]``` slot of `obj` to `false` (blocking new property additions).
   - Marks all existing own property descriptors as `configurable: false` (blocking property deletion or attribute reconfiguration).
   - Leaves `writable` flags untouched on existing data properties (allowing property value updates).
2. **Verification:** `Object.isSealed(obj)` returns `true` if an object is sealed.
3. **Strict Mode Failure:** Attempting to delete a property or add a new property on a sealed object fails silently in non-strict mode, but throws a `TypeError` in strict mode (`"use strict"`).

---

## Key Characteristics
- **Fixed Shape, Mutable State:** Maintains object shape stability in V8 while permitting state updates.
- **Shallow Action:** Like `Object.freeze()`, `Object.seal()` operates **shallowly**. Nested objects remain completely unsealed unless explicitly sealed recursively.

---

## Common Mistakes

### Mistake 1: Expecting `Object.seal()` to prevent value mutations
Developers often confuse `seal()` with `freeze()` and expect property values to remain read-only.

```javascript
const user = Object.seal({ name: "Luffy", score: 100 });

// VALID! Values CAN be updated on sealed objects:
user.score = 200; 
console.log(user.score); // Output: 200

// BLOCKED: Adding new properties fails or throws TypeError in strict mode
// user.role = "Captain"; 
```

### Mistake 2: Assuming `Object.seal()` seals nested objects
Sealing an object containing a nested object does not seal the nested child object.

---

## Best Practices
- **Use `Object.seal()` for stateful domain entities** where property keys must remain fixed to preserve V8 shape optimizations, but values need to update during runtime.
- **Use `Object.isSealed(obj)`** to verify if an object's property layout is sealed.
- **Enforce strict mode** so illegal property additions or deletions on sealed objects throw explicit runtime errors.

---

## Comparison Table

| Feature / Attribute | `preventExtensions()` | `Object.seal()` | `Object.freeze()` |
| :--- | :--- | :--- | :--- |
| **Add New Keys** | ❌ Blocked | ❌ Blocked | ❌ Blocked |
| **Delete Existing Keys**| ✅ **Allowed** | ❌ **Blocked** | ❌ **Blocked** |
| **Modify Existing Values**| ✅ **Allowed** | ✅ **Allowed** | ❌ **Blocked** |
| **`configurable` Flag** | Untouched | **Set to `false`** | **Set to `false`** |
| **`writable` Flag** | Untouched | Untouched | **Set to `false`** |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates Object.seal() mechanics, value mutations, blocked deletions,
 * strict mode behavior, and Object.isSealed() verification.
 */
"use strict";

// 1. Instantiating a Sealed Object
const playerSession = Object.seal({
  id: "player_101",
  score: 500,
  level: 1
});

console.log("Is Sealed?:", Object.isSealed(playerSession)); // Expected: true
console.log("Is Frozen?:", Object.isFrozen(playerSession)); // Expected: false (Values writable)

// 2. Updating Existing Property Values (ALLOWED)
playerSession.score = 750;
playerSession.level = 2;
console.log("Updated Score:", playerSession.score); // Expected: 750

// 3. Attempting to Add New Property (BLOCKED in strict mode)
try {
  playerSession.rank = "Gold";
} catch (err) {
  console.log("Blocked Property Addition Error:", err.message);
  // Expected Output: Cannot add property rank, object is not extensible
}

// 4. Attempting to Delete Existing Property (BLOCKED in strict mode)
try {
  delete playerSession.score;
} catch (err) {
  console.log("Blocked Property Deletion Error:", err.message);
  // Expected Output: Cannot delete property 'score' of #<Object>
}

// 5. Inspecting Descriptor Attributes on Sealed Object
const scoreDescriptor = Object.getOwnPropertyDescriptor(playerSession, "score");
console.log("Sealed Property Descriptor:", scoreDescriptor);
/* Expected Output:
{
  value: 750,
  writable: true,        // Writable remains TRUE!
  enumerable: true,
  configurable: false    // Configurable set to FALSE!
}
*/
```

---

## Key Takeaways
- `Object.seal()` locks an object's shape by setting `configurable: false` on all properties and blocking new key additions.
- Unlike `Object.freeze()`, sealed objects still allow existing writable properties to be updated.
- Sealing is shallow; nested child objects remain unsealed unless explicitly sealed.
- `Object.isSealed(obj)` checks if an object is sealed.

---

## Related
- [[js-object-freeze]] — Complete breakdown of `Object.freeze()` and `deepFreeze()`.
- [[js-property-descriptors]] — Understanding descriptor attributes (`configurable`, `writable`).
- [[js-v8-fast-properties-vs-dictionary-mode]] — Maintaining shape stability in V8 engines.
- [[MOC - JS Objects & Structures]] — Central Map of Content for Objects.
