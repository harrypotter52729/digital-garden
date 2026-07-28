---
id: 202607280810
title: Property Descriptors (Data vs Accessor Descriptors)
aliases:
  - property descriptors
  - data descriptors
  - accessor descriptors
  - property attributes
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/properties
  - topic/descriptors
date_created: 2026-07-28
mastery_level: 1
---

# Property Descriptors (Data vs Accessor Descriptors)

> **TL;DR:** An object property isn't just a simple value stored in a slot; it's a security-badged locker. The Property Descriptor is the locker's config sheet specifying the stored item, whether the item can be replaced (`writable`), whether the locker appears on public directory maps (`enumerable`), and whether the locker can be removed or reconfigured (`configurable`).

---

## Why This Exists
In simple scripts, object properties appear to be basic key-value pairs. However, building robust frameworks, security-conscious applications, or enterprise libraries requires fine-grained control over property behavior. 

JavaScript engines store metadata attributes alongside every property. **Property Descriptors** expose this low-level metadata, allowing developers to lock properties against unintended modification, hide internal implementation keys during iteration, or execute logic dynamically when properties are accessed.

---

## Mental Model
Think of an object property as an administrative file entry in a corporate database:
- **`value`**: The data inside the file.
- **`writable`**: Toggle switch determining if employees can overwrite the file data.
- **`enumerable`**: Toggle switch determining if the file shows up in public search lists.
- **`configurable`**: Master security lock determining if the file can be deleted or if its permission attributes can be modified later.

```
                  ┌────────────────────────────────────────┐
                  │          PROPERTY DESCRIPTOR           │
                  └───────────────────┬────────────────────┘
                                      │
           ┌──────────────────────────┴──────────────────────────┐
           ▼                                                     ▼
  [ Data Descriptor ]                                 [ Accessor Descriptor ]
  - value: any                                        - get: Function | undefined
  - writable: boolean                                 - set: Function | undefined
  - enumerable: boolean                               - enumerable: boolean
  - configurable: boolean                             - configurable: boolean
```

---

## How It Works

JavaScript categorizes property descriptors into two mutually exclusive types:

### 1. Data Descriptors
Properties that hold an actual value. Contains four attributes:
- `value`: The retrieved value (Defaults to `undefined`).
- `writable`: If `true`, the value can be changed via assignment (Defaults to `false` when defined via `defineProperty`).
- `enumerable`: If `true`, the property appears in `for...in` loops and `Object.keys()` (Defaults to `false`).
- `configurable`: If `true`, the property can be deleted and its descriptor flags can be altered (Defaults to `false`).

### 2. Accessor Descriptors
Properties described by a getter-setter function pair. Contains four attributes:
- `get`: A function called when reading the property (Defaults to `undefined`).
- `set`: A function called when writing to the property (Defaults to `undefined`).
- `enumerable`: Visibility flag during iteration (Defaults to `false`).
- `configurable`: Modification/deletion permission flag (Defaults to `false`).

*Rule:* A descriptor **cannot** combine `value`/`writable` with `get`/`set` simultaneously. Doing so throws a `TypeError`.

---

## Key Characteristics
- **Literal Defaults vs `defineProperty` Defaults:**
  - Object literal definitions (`const o = { a: 1 }`) default `writable`, `enumerable`, and `configurable` to `true`.
  - Defining properties via `Object.defineProperty(o, 'a', { value: 1 })` defaults omitted boolean attributes to `false`!
- **Inspection:** `Object.getOwnPropertyDescriptor(obj, prop)` returns the exact descriptor metadata for an own property.

---

## Common Mistakes

### Mistake 1: Forgetting that omitted flags default to `false` in `defineProperty()`
Developers assume `Object.defineProperty(obj, "key", { value: 10 })` creates a normal editable property.

```javascript
const user = {};
Object.defineProperty(user, "role", { value: "Admin" });

user.role = "Guest"; // FAILS SILENTLY! (throws TypeError in strict mode because 'writable' defaulted to false!)
delete user.role;    // FAILS SILENTLY! ('configurable' defaulted to false!)

console.log(user.role); // Output: "Admin"
```

### Mistake 2: Mixing Data and Accessor Descriptor keys
Combining `value` or `writable` with `get` or `set` in a single descriptor object throws an immediate exception.

```javascript
// INVALID: Throws TypeError: Invalid property descriptor.
Object.defineProperty(obj, "invalid", {
  value: 100,
  get() { return 100; }
});
```

---

## Best Practices
- **Inspect descriptors via `Object.getOwnPropertyDescriptor(obj, key)`** when debugging framework behaviors or frozen objects.
- **Explicitly set boolean flags** (`writable: true`, `enumerable: true`, `configurable: true`) when calling `Object.defineProperty()` to avoid unexpected `false` defaults.
- **Use non-enumerable properties** (`enumerable: false`) for internal library metadata or backing state to avoid cluttering `Object.keys()` outputs.

---

## Comparison Table

| Attribute | Data Descriptor | Accessor Descriptor | Object Literal Default | `defineProperty` Default |
| :--- | :--- | :--- | :--- | :--- |
| **`value`** | Supported | **Forbidden** | Assigned Value | `undefined` |
| **`writable`** | Supported | **Forbidden** | `true` | `false` |
| **`get`** | **Forbidden** | Supported | N/A | `undefined` |
| **`set`** | **Forbidden** | Supported | N/A | `undefined` |
| **`enumerable`** | Supported | Supported | `true` | `false` |
| **`configurable`**| Supported | Supported | `true` | `false` |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates inspecting descriptors, data descriptor protections,
 * accessor descriptors, and default flag behaviors.
 */

const user = { name: "Luffy" };

// 1. Inspecting Object Literal Descriptor Defaults
const nameDescriptor = Object.getOwnPropertyDescriptor(user, "name");
console.log("Literal Descriptor:", nameDescriptor);
/* Expected Output:
{
  value: 'Luffy',
  writable: true,
  enumerable: true,
  configurable: true
}
*/

// 2. Defining a Protected Read-Only Data Property
Object.defineProperty(user, "id", {
  value: 1001,
  writable: false,     // Value cannot change
  enumerable: false,   // Hidden from Object.keys()
  configurable: false  // Cannot be deleted or reconfigured
});

// Attempting mutation
user.id = 9999; 
console.log("Mutated ID Attempt:", user.id); // Expected: 1001 (Mutation blocked)

// Verifying Non-Enumerability
console.log("Object.keys():", Object.keys(user)); // Expected: ["name"] (id is hidden!)

// 3. Defining an Accessor Property (Getter/Setter Descriptor)
let _score = 50;
Object.defineProperty(user, "score", {
  get() {
    return _score;
  },
  set(val) {
    if (val < 0) throw new Error("Score cannot be negative");
    _score = val;
  },
  enumerable: true,
  configurable: true
});

user.score = 85;
console.log("Accessor Value:", user.score); // Expected: 85

// 4. Verifying Built-in Immutable Constant Descriptors (Math.PI)
const piDescriptor = Object.getOwnPropertyDescriptor(Math, "PI");
console.log("Math.PI Descriptor:", piDescriptor);
/* Expected Output:
{
  value: 3.141592653589793,
  writable: false,
  enumerable: false,
  configurable: false
}
*/
```

---

## Key Takeaways
- Property Descriptors define metadata attributes controlling mutation (`writable`), iteration (`enumerable`), and reconfiguration (`configurable`).
- Descriptors are either **Data Descriptors** (`value`/`writable`) or **Accessor Descriptors** (`get`/`set`), but never both simultaneously.
- Properties created via object literals default all boolean descriptor flags to `true`.
- Properties created via `Object.defineProperty()` default all omitted boolean descriptor flags to `false`.

---

## Related
- [[js-define-property]] — Deep dive into `Object.defineProperty` and batch descriptor APIs.
- [[js-getters-and-setters]] — Working with accessor property syntax and encapsulation.
- [[js-object-freeze]] — How `Object.freeze()` locks descriptor flags under the hood.
- [[MOC - JS Objects & Structures]] — Central Map of Content for Objects.
