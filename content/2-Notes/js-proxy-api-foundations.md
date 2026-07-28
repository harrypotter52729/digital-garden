---
id: 202607280823
title: Proxy API Foundations (get & set Traps)
aliases:
  - Proxy API
  - Proxy get trap
  - Proxy set trap
  - Proxy foundations
  - Proxy object
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/proxy-api
  - topic/metaprogramming
date_created: 2026-07-28
mastery_level: 1
---

# Proxy API Foundations (`get` & `set` Traps)

> **TL;DR:** Imagine hiring a professional security guard to stand in front of your house. Whenever a guest asks to look inside a room (read a property) or leave a package (write a property), the request goes to the security guard first. The guard logs the visit, checks ID cards (data validation), blocks invalid items, and only passes valid requests to your house!

---

## Why This Exists
Standard JavaScript objects execute property reads and writes directly without intervention. If an application needs to detect when properties change (Reactivity in Vue 3/MobX), validate state updates before they occur, log property access for debugging, or return fallback default values for missing keys, wrapping every property in getters and setters becomes tedious.

The **Proxy API** enables metaprogramming by allowing developers to wrap an object with a handler containing **traps** that intercept and override standard low-level object operations transparently.

---

## Mental Model
Think of a Proxy as a transparent wrapper shell around a target object:
- **`target`**: The real underlying object holding the raw data.
- **`handler`**: A trap configuration object specifying custom logic (interceptors).
- **`receiver`**: The proxy object itself (or prototype derived object) passed into traps to preserve `this` binding context.

```
USER / CALLER
     │  Reads `proxy.name` or Writes `proxy.age = 20`
     ▼
[ PROXY OBJECT LAYER ]
     │
     ▼ Traps Intercept Request
[ HANDLER TRAPS ]
  ├── get(target, prop, receiver) ──► Custom Validation / Logging ──► Reflect.get()
  └── set(target, prop, val, rec) ──► Type Checks / Safeguards   ──► Reflect.set()
                                                                         │
                                                                         ▼
                                                               [ TARGET OBJECT ]
```

---

## How It Works

1. **Instantiation:** `const proxy = new Proxy(target, handler)`
   - `target`: Must be an object, function, or array.
   - `handler`: Object containing trap functions matching ECMAScript internal method signatures.
2. **The `get` Trap:** Intercepts property reads (`proxy.prop`, `proxy[prop]`).
   - Signature: `get(target, property, receiver)`
   - Must return the property value or computed result.
3. **The `set` Trap:** Intercepts property assignments (`proxy.prop = value`).
   - Signature: `set(target, property, value, receiver)`
   - **MUST return a boolean:** `true` if assignment succeeded, `false` if assignment failed (which throws a `TypeError` in strict mode).
4. **Reflect Forwarding:** Traps typically forward valid requests to `Reflect.get()` and `Reflect.set()` to preserve native language semantics.

---

## Key Characteristics
- **Transparent Wrapper:** Operations performed on `proxy` trigger traps while leaving `target` code untouched.
- **Identity Disconnect:** `proxy !== target`. The proxy is a distinct wrapper object referencing `target`.
- **Target Invariants Protection:** If a target property is non-configurable and non-writable, a `get` trap **must** return the exact value stored on target, or V8 throws an invariant `TypeError`.

---

## Common Mistakes

### Mistake 1: Forgetting to return `true` from a `set` trap
If a `set` trap performs an assignment but omits `return true`, JS evaluates the return value as `undefined` (`falsy`), causing assignment to throw a `TypeError` in strict mode!

```javascript
"use strict";
const proxy = new Proxy({}, {
  set(target, prop, val) {
    target[prop] = val; 
    // BUG: Missing 'return true;' or 'return Reflect.set(...)';
  }
});

// CRASH! TypeError: 'set' on proxy: trap returned falsish for property 'age'
proxy.age = 25; 
```

### Mistake 2: Accessing `target[prop]` directly inside `get` traps instead of using `Reflect.get()`
Using `target[prop]` inside a `get` trap breaks `this` context binding if the target object contains inherited getters!

```javascript
// BAD: Breaks 'this' when calling getters on prototype chains!
get(target, prop) { return target[prop]; }

// GOOD: Preserves 'this' binding via receiver parameter:
get(target, prop, receiver) { return Reflect.get(target, prop, receiver); }
```

---

## Best Practices
- **Always return `Reflect.get(target, prop, receiver)`** inside `get` traps to preserve prototype getter `this` contexts.
- **Always return `Reflect.set(target, prop, val, receiver)`** or `true` inside `set` traps.
- **Avoid wrapping every data object in a Proxy** because proxies add small runtime instruction overhead. Use them for framework reactivity, validation, and debugging.

---

## Comparison Table

| Attribute | Plain Object | Getters/Setters | Proxy Wrapper |
| :--- | :--- | :--- | :--- |
| **Interception Scope** | None | Specific predefined properties | **Universal (All current & future keys)**|
| **Intercepts Missing Keys**| No (Returns `undefined`)| No | **Yes (Traps ANY property read)** |
| **Target Object Changes** | Direct Data Mutate | Requires method rewriting | **Zero (Wraps target without modifying it)**|
| **Requires `Reflect`** | No | No | **Highly Recommended** |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates Proxy instantiation, get/set traps, validation,
 * missing property interception, and Reflect forwarding.
 */
"use strict";

// 1. Target Data Object
const userTarget = {
  id: 101,
  username: "monkey_luffy",
  _role: "Captain" // Private backing field convention
};

// 2. Handler Object Defining Interception Traps
const userHandler = {
  // Intercept Property Reads
  get(target, property, receiver) {
    console.log(`[LOG] Property READ: '${String(property)}'`);

    // Intercept missing properties with custom fallback
    if (!Reflect.has(target, property)) {
      return `Property '${String(property)}' does not exist`;
    }

    // Forward to Reflect to preserve native semantics and receiver binding
    return Reflect.get(target, property, receiver);
  },

  // Intercept Property Assignments
  set(target, property, value, receiver) {
    console.log(`[LOG] Property WRITE: '${String(property)}' = '${value}'`);

    // Validation Guard: Protect ID from modification
    if (property === "id") {
      throw new Error("Security Violation: Property 'id' is immutable.");
    }

    // Validation Guard: Sanitize username input
    if (property === "username" && (typeof value !== "string" || value.length < 3)) {
      throw new TypeError("Invalid Username: Must be a string with at least 3 characters.");
    }

    // Forward assignment result to Reflect (Returns boolean status)
    return Reflect.set(target, property, value, receiver);
  }
};

// 3. Instantiate Proxy
const userProxy = new Proxy(userTarget, userHandler);

// 4. Executing Property Access
console.log("Username:", userProxy.username); // Logs READ, Expected: "monkey_luffy"

// 5. Intercepting Missing Property Access
console.log("Missing Property Access:", userProxy.age); // Logs READ, Expected: "Property 'age' does not exist"

// 6. Validating Successful Property Write
userProxy.username = "captain_luffy"; // Logs WRITE
console.log("Updated Username:", userProxy.username); // Expected: "captain_luffy"

// 7. Testing Security & Validation Guards
try {
  userProxy.id = 999; // Security exception!
} catch (err) {
  console.log("Caught Guard Exception:", err.message); 
  // Expected: "Security Violation: Property 'id' is immutable."
}

try {
  userProxy.username = "lu"; // Validation exception!
} catch (err) {
  console.log("Caught Validation Exception:", err.message); 
  // Expected: "Invalid Username: Must be a string with at least 3 characters."
}
```

---

## Key Takeaways
- A `Proxy` wraps a target object and intercepts fundamental object operations using handler **traps**.
- The `get` trap intercepts property reads; the `set` trap intercepts property assignments.
- The `set` trap **must return a boolean** (`true` for success, `false` for failure).
- Always use `Reflect.get()` and `Reflect.set()` with the `receiver` parameter inside traps to preserve correct language semantics and `this` bindings.

---

## Related
- [[js-proxy-advanced-traps]] — Deep dive into `has`, `deleteProperty`, `ownKeys`, and `apply` traps.
- [[js-reflect-api]] — Complete guide to the Reflect API namespace.
- [[js-getters-and-setters]] — Comparing Proxy traps vs individual property accessors.
- [[MOC - JS Advanced Mechanics & Metaprogramming]] — Map of Content for Metaprogramming.
