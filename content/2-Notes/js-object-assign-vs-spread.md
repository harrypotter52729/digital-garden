---
id: 202607281951
title: Object.assign() vs Spread Syntax ({...obj})
aliases:
  - Object.assign vs spread
  - spread operator vs assign
  - shallow copy object assign
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/objects
date_created: {today}
mastery_level: 1
---

# `Object.assign()` vs Spread Syntax (`{...obj}`)

> **TL;DR:** Spread `{...obj}` is like taking a quick Polaroid snapshot that copies values directly into a brand-new object shell, whereas `Object.assign(target, src)` acts like a paint roller that modifies an existing target object by triggering its property setters!

---

## Why This Exists
Both `Object.assign()` and the object spread operator (`...`) perform shallow copying of enumerable own properties. However, subtle differences exist regarding setter triggers, prototype inheritance of target objects, and return values. Understanding these differences prevents subtle runtime bugs in state updates.

---

## Mental Model
- **`Object.assign(target, source)` (Paint Roller):** You hold a target container object (which might already have custom getters/setters) and pour property values from the source into it line by line.
- **`{ ...source }` (Fresh Mold):** You drop all properties into a fresh, blank literal `{}` object. Setters on `Object.prototype` are never triggered.

---

## How It Works

1. **`Object.assign(target, ...sources)`:**
   - Invokes ``[[js-ecmascript-internal-slots|[[Set]]]]`` internal operations on the target object.
   - If target properties have setter functions, `Object.assign()` triggers those setters!
   - Returns the mutated target object.
2. **Object Spread `{ ...source }`:**
   - Performs ``[[js-ecmascript-internal-slots|[[DefineOwnProperty]]]]`` internal operations on the newly created object.
   - Bypasses setters entirely, defining raw property values directly.

---

## Key Differences & Comparison Table

| Feature / Trait | `Object.assign(target, src)` | Spread `{ ...src }` |
| :--- | :--- | :--- |
| **Operation Type** | ``[[js-ecmascript-internal-slots|[[Set]]]]`` (Triggers setters) | ``[[js-ecmascript-internal-slots|[[DefineOwnProperty]]]]`` (Bypasses setters) |
| **Mutates Target?** | Yes, mutates the 1st argument | No, always creates a new object |
| **Null / Undefined Source**| Silently ignores them | Silently ignores them in `{ ...null }` |
| **Prototypes** | Can copy into custom prototype targets | Always creates plain `Object.prototype` |

---

## Common Mistakes

### Mistake: Assuming both trigger setters identically
```javascript
const target = {
  set name(val) { console.log("Setter Called:", val); }
};

// Object.assign triggers the setter!
Object.assign(target, { name: "Alice" }); // Logs: "Setter Called: Alice"

// Spread creates a new object and bypasses target setters!
const clone = { ...target, name: "Alice" }; // No setter called!
```

---

## Canonical Code Example

```javascript
/**
 * Demonstrates operational differences between Object.assign() 
 * and Object Spread {...src} regarding mutation and setters.
 */

// 1. Existing Target Object with a Setter
const targetObject = {
  _val: 0,
  set score(newScore) {
    console.log(`[Setter Triggered] Setting score from ${this._val} to ${newScore}`);
    this._val = newScore;
  }
};

const sourceData = { score: 100, level: 5 };

// 2. Object.assign() Mutates Target & Triggers Setters
console.log("--- Object.assign Run ---");
const assignResult = Object.assign(targetObject, sourceData);

console.log("Target Mutated?", assignResult === targetObject); // Expected: true
console.log("Target Score Value:", targetObject._val);           // Expected: 100

// 3. Object Spread Creates Fresh Copy & Bypasses Setters
console.log("
--- Object Spread Run ---");
const freshCopy = { ...targetObject, ...sourceData };

console.log("Fresh Object Mutates Target?", freshCopy === targetObject); // Expected: false
console.log("Fresh Copy Score Property:", freshCopy.score);              // Expected: 100
```

---

## Key Takeaways
- Both tools perform **shallow copies** (nested reference objects remain shared).
- Use `{ ...obj }` for immutable state updates (React, Redux) where creating new object instances is desired.
- Use `Object.assign(target, src)` when mutating an existing target object in place is intended.

---

## Related
- [[js-shallow-vs-deep-copy]] — Overview of shallow vs deep copying.
- [[js-structured-clone]] — Native deep cloning via `structuredClone()`.
- [[js-property-descriptors]] — Understanding getters, setters, and ``[[js-ecmascript-internal-slots|[[DefineOwnProperty]]]]``.
- [[MOC - JS Objects & Structures]] — Central Objects MOC.
