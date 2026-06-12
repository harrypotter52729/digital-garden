---
id: 202606120838
title: Object.freeze vs Object.seal
aliases:
  - object-immutability
  - freeze-seal
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/objects
date_created: 2026-06-12
mastery_level: 1
---

# Object.freeze vs Object.seal

> **TL;DR:** **Freeze** is like putting a statue inside a **solid block of ice** (you cannot add, remove, or change any details). **Seal** is like **locking a diary** with a padlock (you cannot add new pages or tear out old ones, but you *can* scribble and edit the words on the existing pages).

## The Mental Model: Const is Not Immutable
Many developers think declaring an object with `[[js-const-mutabilty|const]]` makes it read-only. This is false:

```javascript
const user = { name: "Hemanth" };
user.name = "John"; // Works fine!
```

`const` only locks the **variable box** (preventing reassignment to a new object). To lock the **content values** inside the object itself (protecting [[js-primitive-vs-reference-types|reference types]]), you need `Object.freeze()` or `Object.seal()`.

---

## The Two Levels of Immutability

| Action | Normal Object | Sealed Object (`Object.seal`) | Frozen Object (`Object.freeze`) |
| :--- | :---: | :---: | :---: |
| **Add New Keys?** | Yes | No | No |
| **Delete Keys?** | Yes | No | No |
| **Modify Values?**| Yes | **Yes** | No |

---

## 1. Object.seal(obj)
- **What it does:** Prevents adding new properties and makes all existing properties non-configurable (they cannot be deleted or changed to getters/setters).
- **Modification Allowed:** You can still read and write to existing properties.

---

## 2. Object.freeze(obj)
- **What it does:** The ultimate lock. It does everything `Object.seal()` does, plus it sets all property descriptors to `writable: false`. Existing properties cannot be changed.

---

## The Catch: Shallow Immutability
Both `freeze` and `seal` are **shallow**. If your object has nested objects, the nested objects remain completely unlocked!

```javascript
const config = { database: { host: "localhost" } };
Object.freeze(config);

config.database.host = "production"; // Works! The sub-object was NOT frozen.
```
To achieve true deep immutability, you must recursively freeze every sub-object (a "deep freeze").

---

## Canonical Code Example

Here is a script demonstrating how seal and freeze act, alongside a helper utility for recursive deep freezing:

```javascript
// --- 1. Object.seal Example ---
const userRecord = { username: "hemanth123", loginCount: 5 };
Object.seal(userRecord);

userRecord.loginCount = 6;     // Works (existing values can change)
userRecord.isAdmin = true;     // Silent fail (or TypeError in strict mode)
delete userRecord.username;    // Silent fail (or TypeError in strict mode)

console.log("Sealed Object:", userRecord); // { username: "hemanth123", loginCount: 6 }


// --- 2. Object.freeze Example ---
const apiConfig = { url: "https://api.com", timeout: 5000 };
Object.freeze(apiConfig);

apiConfig.timeout = 10000;     // Silent fail
apiConfig.port = 8080;         // Silent fail
delete apiConfig.url;          // Silent fail

console.log("Frozen Object:", apiConfig); // { url: "https://api.com", timeout: 5000 }


// --- 3. Advanced: Deep Freeze Helper ---
function deepFreeze(obj) {
  // Get all property names
  const propNames = Object.getOwnPropertyNames(obj);

  // Freeze properties before freezing the parent
  for (const name of propNames) {
    const value = obj[name];
    if (value && typeof value === "object") {
      deepFreeze(value); // Recursive call
    }
  }

  return Object.freeze(obj);
}

const secureConfig = deepFreeze({
  server: { host: "127.0.0.1", ports: [80, 443] }
});

secureConfig.server.host = "10.0.0.1"; // Safely blocked!
console.log("Deep Frozen Host:", secureConfig.server.host); // "127.0.0.1"
```

---

## Related
* [[js-const-mutabilty]] - Const reassignment constraints on references.
* [[MOC - JS Objects & Structures]] - Hub for object structure properties.
* [[js-primitive-vs-reference-types]] - Understanding mutable reference objects.
