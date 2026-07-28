---
id: 202607280837
title: Object Protection via Object.preventExtensions()
aliases:
  - Object.preventExtensions
  - non-extensible objects
  - preventExtensions
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/immutability
  - topic/security
date_created: 2026-07-28
mastery_level: 1
---

# Object Protection via `Object.preventExtensions()`

> **TL;DR:** Calling `Object.preventExtensions(obj)` is like locking the front door of a warehouse so no new inventory boxes can be carried in. However, workers inside the warehouse can still move existing boxes around, re-label them (update values), or throw existing unwanted boxes out into the trash (delete properties)!

---

## Why This Exists
In application architecture, developers often want to prevent accidental addition of unsupported properties (e.g., preventing typos in configuration keys like `config.timeoutt = 5000`), while still allowing existing configuration values to be edited or deleted.

`Object.preventExtensions()` is the fundamental first tier of object protection in JavaScript. It locks an object's extensibility slot, preventing new properties from ever being attached to the object, while leaving all existing property capabilities intact.

---

## Mental Model
Think of the three levels of built-in object lockouts:
- **`preventExtensions()`**: Locks the entrance door (No new properties allowed). Existing properties can be modified OR deleted.
- **`seal()`**: Locks entrance door AND locks existing rooms in place (No new properties, no deleting existing properties). Existing values can be modified.
- **`freeze()`**: Locks entrance door, locks rooms, AND freezes all data (No adding, no deleting, no modifying).

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

1. **Extensibility Modification:** Calling `Object.preventExtensions(obj)` sets the internal ```[[js-ecmascript-internal-slots|[[Extensible]]]]``` slot of `obj` to `false`.
2. **Property Creation Interception:** Any subsequent attempt to add a new property via dot notation (`obj.newKey = val`), bracket notation, or `Object.defineProperty()` fails:
   - In non-strict mode: Fails silently.
   - In strict mode (`"use strict"`): Throws a `TypeError: Cannot add property newKey, object is not extensible`.
3. **Existing Property Attributes Untouched:** Existing properties retain their exact `writable`, `enumerable`, and `configurable` descriptor flags. Existing keys can still be deleted via `delete obj.key`.
4. **Verification:** `Object.isExtensible(obj)` returns `false` if an object is non-extensible.

---

## Key Characteristics
- **Irreversible Operation:** Once an object is marked non-extensible, it can **never** be made extensible again during its runtime lifecycle.
- **Prototype Chain Interaction:** Prevents adding own properties directly to `obj`, but if `obj`'s prototype is updated, inherited properties on the prototype still resolve.

---

## Common Mistakes

### Mistake 1: Assuming `preventExtensions()` prevents property deletion
Developers assume `preventExtensions()` locks existing properties against deletion.

```javascript
const user = Object.preventExtensions({ name: "Luffy", age: 19 });

// VALID! Deletion IS allowed on non-extensible objects:
delete user.age; 
console.log(user.age); // Output: undefined

// BLOCKED: Adding new properties fails or throws TypeError in strict mode
// user.role = "Captain";
```

---

## Best Practices
- **Use `Object.preventExtensions()` in defensive config managers** to prevent typos when setting configuration flags without locking down value edits.
- **Use `Object.isExtensible(obj)`** to check if an object allows new property additions before calling `Object.defineProperty()`.

---

## Comparison Table

| Attribute / Feature | `preventExtensions()` | `Object.seal()` | `Object.freeze()` |
| :--- | :--- | :--- | :--- |
| **Add New Keys** | ❌ Blocked | ❌ Blocked | ❌ Blocked |
| **Delete Existing Keys**| ✅ **Allowed** | ❌ Blocked | ❌ Blocked |
| **Modify Existing Values**| ✅ **Allowed** | ✅ Allowed | ❌ Blocked |
| **`configurable` Flag** | Untouched | Set to `false` | Set to `false` |
| **`writable` Flag** | Untouched | Untouched | Set to `false` |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates Object.preventExtensions(), adding vs deleting properties,
 * strict mode protection, and Object.isExtensible() inspection.
 */
"use strict";

// 1. Instantiating a Non-Extensible Object
const appSettings = Object.preventExtensions({
  theme: "dark",
  timeout: 5000
});

console.log("Is Extensible?:", Object.isExtensible(appSettings)); // Expected: false
console.log("Is Sealed?:", Object.isSealed(appSettings));         // Expected: false (Deletion allowed)

// 2. Modifying Existing Values (ALLOWED)
appSettings.theme = "light";
console.log("Updated Theme:", appSettings.theme); // Expected: "light"

// 3. Deleting Existing Properties (ALLOWED)
delete appSettings.timeout;
console.log("Timeout After Delete:", appSettings.timeout); // Expected: undefined

// 4. Attempting to Add New Property (BLOCKED in strict mode)
try {
  appSettings.newFeature = true;
} catch (err) {
  console.log("Blocked Extension Error:", err.message);
  // Expected Output: Cannot add property newFeature, object is not extensible
}

// 5. Inspecting Descriptor Attributes
const themeDescriptor = Object.getOwnPropertyDescriptor(appSettings, "theme");
console.log("Theme Property Descriptor:", themeDescriptor);
/* Expected Output:
{
  value: 'light',
  writable: true,        // Writable remains TRUE!
  enumerable: true,
  configurable: true     // Configurable remains TRUE!
}
*/
```

---

## Key Takeaways
- `Object.preventExtensions()` prevents new properties from ever being added to an object.
- Existing properties can still be updated and deleted unless sealed or frozen.
- Once marked non-extensible, an object cannot be made extensible again.
- Use `Object.isExtensible(obj)` to verify extensibility status.

---

## Related
- [[js-object-seal]] — `Object.seal()` mechanics preventing property deletions.
- [[js-object-freeze]] — Complete object freezing with `Object.freeze()`.
- [[js-property-descriptors]] — Understanding property descriptor metadata.
- [[MOC - JS Objects & Structures]] — Central Map of Content for Objects.
