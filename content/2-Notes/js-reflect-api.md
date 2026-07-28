---
id: 202607280822
title: The Reflect API
aliases:
  - Reflect API
  - Reflect object
  - Reflect methods
  - Reflection in JavaScript
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/reflect-api
  - topic/metaprogramming
date_created: 2026-07-28
mastery_level: 1
---

# The Reflect API

> **TL;DR:** Before ES6, performing low-level object operations in JavaScript was a messy mix of operators (`delete obj.x`, `"x" in obj`), functions (`Object.defineProperty`), and function methods (`fn.apply()`). The `Reflect` API is a unified, standardized Swiss Army knife namespace providing functional, return-value-friendly versions of every fundamental internal object operation!

---

## Why This Exists
Before ES6, JavaScript lacked a clean, unified API for object reflection. Some low-level operations threw errors on failure (`Object.defineProperty`), others returned booleans (`delete`), some were syntax operators (`in`, `delete`), and others were static `Object` methods.

ES6 introduced `Reflect`—a built-in namespace object (similar to `Math`) containing functional equivalents of all internal object operations. `Reflect` standardizes return values (returning `true`/`false` status indicators instead of throwing exceptions), cleans up metaprogramming syntax, and acts as the perfect companion to the `Proxy` API.

---

## Mental Model
Think of JavaScript internal engine algorithms as a complex factory control panel:
- **Pre-ES6:** Switches, knobs, and buttons were scattered across different rooms (operators, `Object` methods, `Function.prototype`).
- **Reflect API:** A clean, centralized control box with 13 standardized switches labeled `Reflect.get()`, `Reflect.set()`, `Reflect.has()`, `Reflect.deleteProperty()`, etc. Flipping a switch gives you a clear green light (`true`) or red light (`false`) indicating whether the operation succeeded.

```
TRADITIONAL SYNTAX (Scattered)                REFLECT API (Standardized Functional)
delete obj.key        ────────────────────►   Reflect.deleteProperty(obj, "key")
"key" in obj          ────────────────────►   Reflect.has(obj, "key")
fn.apply(ctx, args)   ────────────────────►   Reflect.apply(fn, ctx, args)
new Class(...args)    ────────────────────►   Reflect.construct(Class, args)
```

---

## How It Works

1. **Namespace Object:** `Reflect` is a plain global built-in object. It is **not** a function or constructor; calling `new Reflect()` throws a `TypeError`.
2. **13 Standard Methods:** `Reflect` mirrors the 13 internal ECMAScript Proxy trap methods:
   - `Reflect.get(target, property, [receiver])`: Reads property value.
   - `Reflect.set(target, property, value, [receiver])`: Assigns value, returns `true` if successful.
   - `Reflect.has(target, property)`: Functional equivalent of `property in target`.
   - `Reflect.deleteProperty(target, property)`: Functional equivalent of `delete target[property]`.
   - `Reflect.ownKeys(target)`: Returns ALL own keys (Strings + Symbols, Enumerable + Non-enumerable).
   - `Reflect.defineProperty(target, property, descriptor)`: Defines property, returns `true`/`false` instead of throwing.
   - `Reflect.construct(target, argsArray, [newTarget])`: Programmatic `new` invocation.
   - `Reflect.apply(targetFn, thisArgument, argsArray)`: Programmatic `fn.apply()` execution.
   - `Reflect.getPrototypeOf()`, `Reflect.setPrototypeOf()`, `Reflect.preventExtensions()`, `Reflect.isExtensible()`, `Reflect.getOwnPropertyDescriptor()`.

---

## Key Characteristics
- **Clean Boolean Statuses:** Methods like `Reflect.set()` and `Reflect.defineProperty()` return `true` on success and `false` on failure, eliminating `try...catch` blocks for object modifications.
- **`receiver` Argument Support:** `Reflect.get()` and `Reflect.set()` accept an optional 3rd/4th `receiver` parameter, ensuring correct `this` binding execution when navigating prototype getters and Proxy handlers.
- **Proxy Companion:** `Reflect` methods match Proxy trap signatures 1-to-1, allowing proxy handlers to forward default operations cleanly.

---

## Common Mistakes

### Mistake 1: Instantiating `Reflect` with `new`
`Reflect` is a namespace object, not a constructor.

```javascript
// WRONG: Throws TypeError: Reflect is not a constructor
const r = new Reflect(); 
```

### Mistake 2: Using `Reflect` when standard syntax is cleaner
Using `Reflect.get(user, "name")` in normal application logic instead of `user.name` adds unneeded complexity.

---

## Best Practices
- **Always use `Reflect` methods inside `Proxy` traps** to forward operations safely while preserving correct `this`/receiver semantics.
- **Use `Reflect.ownKeys(obj)`** to inspect every single own key on an object (combining string keys and Symbol keys).
- **Use `Reflect.defineProperty()`** in defensive code to test property assignment success without catching `TypeError` exceptions.

---

## Comparison Table

| Operation | Traditional Syntax / Method | Reflect API Equivalent | Reflect Return Type |
| :--- | :--- | :--- | :--- |
| **Property Access** | `obj.prop` | `Reflect.get(obj, "prop", [receiver])` | Property Value |
| **Property Assignment** | `obj.prop = val` | `Reflect.set(obj, "prop", val, [receiver])` | **Boolean (`true`/`false`)**|
| **Existence Check** | `"prop" in obj` | `Reflect.has(obj, "prop")` | Boolean (`true`/`false`) |
| **Delete Property** | `delete obj.prop` | `Reflect.deleteProperty(obj, "prop")` | Boolean (`true`/`false`) |
| **Define Property** | `Object.defineProperty(...)` (Throws)| `Reflect.defineProperty(...)` | **Boolean (`true`/`false`)**|
| **All Own Keys** | Mixed `keys()` + `getOwnPropertySymbols`| `Reflect.ownKeys(obj)` | Array `[Strings, Symbols]` |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates major Reflect API methods, return value handling,
 * receiver semantics, and Reflect.ownKeys() inspection.
 */

// 1. Defining Target Object with Prototype & Getter
const parent = {
  get greeting() {
    return `Hello, ${this.name}!`;
  }
};

const user = Object.create(parent);
user.name = "Monkey D. Luffy";
const SECRET_KEY = Symbol("secret");
user[SECRET_KEY] = "shh_top_secret";

// 2. Reflect.get() with Receiver Binding Demonstration
console.log("Standard Get:", Reflect.get(user, "name")); // Expected: "Monkey D. Luffy"

// Receiver parameter ensures 'this' inside parent getter resolves to custom receiver object
const customReceiver = { name: "Roronoa Zoro" };
console.log("Reflect.get with Receiver:", Reflect.get(user, "greeting", customReceiver)); 
// Expected Output: "Hello, Roronoa Zoro!"

// 3. Reflect.set() returning Boolean Status
const nonWritableObj = {};
Object.defineProperty(nonWritableObj, "id", { value: 101, writable: false });

const success = Reflect.set(nonWritableObj, "id", 999);
console.log("Assignment Succeeded?:", success); // Expected: false (Clean boolean result, no crash!)

// 4. Reflect.has() and Reflect.deleteProperty()
console.log("Reflect.has ('name'):", Reflect.has(user, "name")); // Expected: true
const deleteSuccess = Reflect.deleteProperty(user, "name");
console.log("Delete Succeeded?:", deleteSuccess); // Expected: true
console.log("Reflect.has ('name') After Delete:", Reflect.has(user, "name")); // Expected: false

// 5. Reflect.ownKeys() Complete Inspection
Object.defineProperty(user, "hiddenCode", { value: 42, enumerable: false });

const allOwnKeys = Reflect.ownKeys(user);
console.log("Reflect.ownKeys():", allOwnKeys); 
// Expected Output: ["hiddenCode", Symbol(secret)] (Extracts non-enumerable AND Symbol keys!)

// 6. Programmatic Construction via Reflect.construct()
function UserClass(name, role) {
  this.name = name;
  this.role = role;
}

const constructedUser = Reflect.construct(UserClass, ["Nami", "Navigator"]);
console.log("Constructed Instance:", constructedUser.name, constructedUser.role); 
// Expected Output: "Nami" "Navigator"
```

---

## Key Takeaways
- `Reflect` is an ES6 built-in namespace object providing 13 standardized methods for object reflection.
- `Reflect` methods return clean boolean statuses (`true`/`false`) on mutation operations rather than throwing exceptions.
- `Reflect.ownKeys(obj)` returns an array containing ALL own keys (Strings + Symbols, Enumerable + Non-enumerable).
- `Reflect` is the companion to `Proxy`, ensuring default object operations forward cleanly with correct receiver (`this`) contexts.

---

## Related
- [[js-proxy-api-foundations]] — Using Reflect within Proxy `get` and `set` traps.
- [[js-proxy-advanced-traps]] — Advanced traps (`has`, `deleteProperty`, `ownKeys`).
- [[js-property-descriptors]] — Property metadata and descriptors.
- [[MOC - JS Advanced Mechanics & Metaprogramming]] — Map of Content for Metaprogramming.
