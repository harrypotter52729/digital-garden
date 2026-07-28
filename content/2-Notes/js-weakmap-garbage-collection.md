---
id: 202607280826
title: WeakMap Mechanics & Garbage Collection
aliases:
  - WeakMap
  - weak references
  - garbage collection weakmap
  - memory leak prevention weakmap
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/data-structures
  - topic/memory
date_created: 2026-07-28
mastery_level: 1
---

# WeakMap Mechanics & Garbage Collection

> **TL;DR:** Storing an object inside a standard `Map` is like chaining a dog to a post—the dog cannot run away (be garbage collected) even if the owner leaves. Storing an object as a key in a `WeakMap` is like following the dog with a shadow—the moment the owner walks away and releases the dog's leash, the dog and its shadow vanish automatically!

---

## Why This Exists
In long-running JavaScript applications, single-page apps, and test automation frameworks, attaching temporary metadata to primary objects (e.g., storing click counters on DOM elements or session tokens on Playwright `Page` objects) is common.

If you store an object as a key in a standard `Map` or plain object, that collection maintains a **Strong Reference** to the object. Even if the rest of your application sets `user = null`, the garbage collector **cannot** reclaim the object's memory because the `Map` is still holding onto it. This is a primary source of severe **Memory Leaks**.

`WeakMap` resolves memory leaks by holding **Weak References** to object keys, allowing the garbage collector to reclaim key objects automatically when no other strong references remain.

---

## Mental Model
Think of memory reachability:
- **Strong Reference (`Map`):** A steel cable anchored to an object. As long as the `Map` exists, the garbage collector cannot sweep the object.
- **Weak Reference (`WeakMap`):** A laser pointer pointing to an object. It tracks data attached to the object, but if the steel cables holding the object snap, the object is swept by Garbage Collection (GC) and the laser target disappears silently.

```
STRONG REFERENCE (Standard Map) ──► Prevents GC
[ Application Code ] ──► (Strong Ref) ──┐
                                        ├──► [ Object 0x100 ] (CANNOT BE FREED!)
[ Map Collection ]   ──► (Strong Ref) ──┘

WEAK REFERENCE (WeakMap) ──► Allows GC
[ Application Code ] ──► (Strong Ref) ──► [ Object 0x100 ]
                                                ▲
[ WeakMap ]          ──► (Weak Ref)  ───────────┘
Setting `user = null` removes the ONLY strong ref ──► GC Sweeps Object & WeakMap Entry!
```

---

## How It Works

1. **Weak Key Constraints:** Keys in a `WeakMap` **must be non-primitive objects** (or registered symbols in ES2023+). Primitives are immutable values without reference lifetimes and cannot be used as `WeakMap` keys.
2. **Weak Reference Semantics:** `WeakMap` does not prevent its key objects from being garbage collected. When an object used as a key loses all external strong references:
   - The key object is marked unreachable by V8's Mark-and-Sweep Garbage Collector.
   - The key object AND its corresponding value inside the `WeakMap` are automatically reclaimed during the next GC cycle.
3. **No Iteration or `.size`:** Because GC execution is non-deterministic (managed asynchronously by V8), exposing `.keys()`, `.values()`, `.entries()`, `for...of`, or `.size` would produce unpredictable runtime behavior. Therefore, `WeakMap` is **intentionally non-iterable**.

---

## Key Characteristics
- **Only 4 APIs Supported:** `.get(key)`, `.set(key, value)`, `.has(key)`, `.delete(key)`.
- **Automatic Garbage Collection:** Automatically cleans up metadata entries when key objects are garbage collected.
- **Private Data Pattern:** Excellent for storing truly private instance state in classes or attaching metadata to external objects without polluting their keys.

---

## Common Mistakes

### Mistake 1: Attempting to use primitive values as `WeakMap` keys
Passing a string or number as a key throws an immediate `TypeError`.

```javascript
const wm = new WeakMap();

// CRASH! TypeError: Invalid value used as weak map key
wm.set("session_id", { active: true }); 

// CORRECT: Use an object reference as the key
const sessionObj = { id: "session_id" };
wm.set(sessionObj, { active: true });
```

### Mistake 2: Expecting to loop through or count `WeakMap` entries
Attempting to read `wm.size` returns `undefined`, and `wm.keys()` does not exist.

---

## Best Practices
- **Use `WeakMap` when attaching hidden metadata** to DOM elements, Playwright `Page` objects, or external API objects whose lifecycle is managed elsewhere.
- **Use `WeakMap` for private class fields** in environments lacking native `#field` syntax.
- **Use `Map` instead of `WeakMap`** if you need to iterate keys, count entries, or use primitive strings as keys.

---

## Comparison Table

| Metric / Feature | `Map` Collection | `WeakMap` Collection |
| :--- | :--- | :--- |
| **Supported Key Types** | Primitives, Objects, Functions | **Non-Primitive Objects Only** |
| **Key Reference Type** | **Strong Reference** (Prevents GC) | **Weak Reference** (Allows GC) |
| **Garbage Collection** | Retains objects until deleted | **Automatic Cleanup when unreferenced**|
| **Iterable (`for...of`)** | ✅ Yes | ❌ **No (Intentionally Blocked)** |
| **`.size` Property** | ✅ Yes (`O(1)`) | ❌ **Undefined** |
| **Primary Use Case** | Caches, Lookup tables, Registries | Private Data, Object Metadata |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates WeakMap mechanics, automatic garbage collection behavior,
 * private data patterns, and DOM/Page metadata tracking.
 */

// 1. Instantiating a WeakMap for Hidden Metadata
const pageMetadata = new WeakMap();

// 2. Simulating External Lifecycle Objects (e.g. Playwright Page Instances)
let loginPage = { url: "https://app.com/login", id: "page_001" };
let dashboardPage = { url: "https://app.com/dashboard", id: "page_002" };

// 3. Attaching Secret Metadata without Polluting Page Objects
pageMetadata.set(loginPage, {
  renderTimestamp: Date.now(),
  retryCount: 0,
  authToken: "secret_token_123"
});

pageMetadata.set(dashboardPage, {
  renderTimestamp: Date.now(),
  retryCount: 2,
  authToken: "secret_token_456"
});

// Reading Metadata
console.log("Login Page Retry Count:", pageMetadata.get(loginPage).retryCount); // Expected: 0
console.log("Has Login Metadata?:", pageMetadata.has(loginPage)); // Expected: true

// Verifying Object Immutability/Pollution Immunity
console.log("Raw Login Page Keys:", Object.keys(loginPage)); 
// Expected Output: ["url", "id"] (loginPage object was NOT polluted with metadata keys!)

// 4. Simulating Garbage Collection & Lifecycle Cleanup
console.log("Closing Login Page...");
// Releasing the ONLY strong reference to loginPage object 0x001
loginPage = null; 

// At this point:
// The object 0x001 is unreachable from root execution stack.
// V8 Garbage Collector will sweep object 0x001 AND its WeakMap entry automatically!

// dashboardPage remains active & accessible
console.log("Dashboard Active:", pageMetadata.has(dashboardPage)); // Expected: true

// 5. Private Class Data Pattern (Pre-ESNext #fields)
const _privateState = new WeakMap();

class SecureUser {
  constructor(username, password) {
    this.username = username;
    // Store sensitive state in WeakMap bound to 'this' instance pointer
    _privateState.set(this, { password });
  }

  validatePassword(input) {
    return _privateState.get(this).password === input;
  }
}

const user = new SecureUser("luffy", "gear5_secret");
console.log("Public Username:", user.username); // Expected: "luffy"
console.log("Direct Password Access:", user.password); // Expected: undefined (Private!)
console.log("Validation Result:", user.validatePassword("gear5_secret")); // Expected: true
```

---

## Key Takeaways
- `WeakMap` holds **weak references** to object keys, preventing memory leaks by allowing unreferenced keys to be garbage collected.
- Keys **must be objects**; values can be any type.
- `WeakMap` is non-iterable and does not expose a `.size` property because GC execution is non-deterministic.
- Ideal for attaching metadata to external objects (DOM elements, Page models) and building private class state.

---

## Related
- [[js-weakset-object-tracking]] — Memory-safe sets for tracking object references.
- [[js-map-vs-object]] — Selecting between Map, Object, and WeakMap.
- [[js-garbage-collection-mark-and-sweep]] — Deep dive into V8 Mark-and-Sweep garbage collection algorithms.
- [[MOC - JS Data Types & Memory]] — Central Map of Content for Memory Management.
