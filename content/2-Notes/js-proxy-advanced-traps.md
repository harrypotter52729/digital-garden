---
id: 202607280824
title: Advanced Proxy Traps (has, deleteProperty, ownKeys, apply)
aliases:
  - Proxy advanced traps
  - Proxy has trap
  - Proxy deleteProperty trap
  - Proxy ownKeys trap
  - Proxy apply trap
  - defensive proxy
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/proxy-api
  - topic/security
date_created: 2026-07-28
mastery_level: 1
---

# Advanced Proxy Traps (`has`, `deleteProperty`, `ownKeys`, `apply`)

> **TL;DR:** While basic `get` and `set` traps control reading and writing data, Advanced Proxy Traps act as full security clearance guards. The `has` trap hides secret rooms from property scans (`"password" in obj`), `deleteProperty` blocks unauthorized deletion, `ownKeys` sanitizes directory listings (`Object.keys()`), and `apply` monitors function calls!

---

## Why This Exists
Simple applications only intercept reading and writing values. However, building enterprise automation frameworks, defensive security barriers, mock objects, or API test wrappers requires controlling **all** interaction pathways.

Without advanced traps, an unauthorized user could bypass a `get` trap by checking `"secret" in obj`, running `delete obj.protectedKey`, or enumerating raw keys via `Object.keys(obj)`. Advanced Proxy traps close these backdoors, providing 360-degree object interception.

---

## Mental Model
Think of an object as a confidential corporate building:
- **`has` Trap (`in` operator):** Intercepts the security desk query: *"Is there an office named 'Vault' in this building?"* The trap can lie and answer `false`.
- **`deleteProperty` Trap (`delete`):** Intercepts demolition crews trying to delete an office room.
- **`ownKeys` Trap (`Object.keys()`):** Intercepts auditors requesting a complete building directory list, filtering out secret rooms.
- **`apply` Trap (`fn()`):** Intercepts when an employee attempts to run an internal executive function.

```
                  ┌────────────────────────────────────────┐
                  │             PROXY HANDLER              │
                  └───────────────────┬────────────────────┘
                                      │
    ┌────────────────┬────────────────┼────────────────┬────────────────┐
    ▼                ▼                ▼                ▼                ▼
[ has ]          [ deleteProperty ] [ ownKeys ]      [ apply ]        [ construct ]
Intercepts:      Intercepts:        Intercepts:      Intercepts:      Intercepts:
`key in obj`     `delete obj.key`   `Object.keys()`  `fn(...args)`    `new Class()`
```

---

## How It Works

### 1. The `has(target, property)` Trap
- Intercepts the `in` operator (e.g., `"token" in proxy`).
- **MUST return a boolean.** Returning `false` hides the property from `in` checks even if it exists on `target`.

### 2. The `deleteProperty(target, property)` Trap
- Intercepts property deletion (e.g., `delete proxy.key`).
- **MUST return a boolean.** Returns `true` if deletion succeeded, `false` if blocked (throws `TypeError` in strict mode).

### 3. The `ownKeys(target)` Trap
- Intercepts `Object.keys()`, `Object.getOwnPropertyNames()`, `Object.getOwnPropertySymbols()`, and `Reflect.ownKeys()`.
- **MUST return an Array or Iterable** of string and symbol keys.
- *Invariant Constraint:* Must return all non-configurable own properties present on `target`, or V8 throws an invariant `TypeError`.

### 4. The `apply(targetFn, thisArg, argumentsList)` Trap
- Intercepts function invocations (e.g., `proxyFn(...args)`, `fn.call()`, `fn.apply()`).
- Target **must be a callable function**.

---

## Key Characteristics
- **Complete Encapsulation:** Enables fully virtualized or defensive objects where secret attributes exist internally but are invisible to all external inspection APIs.
- **Function Mocking & Spying:** The `apply` and `construct` traps allow building spy wrappers that record call arguments, execution counts, and return values without modifying original functions.

---

## Common Mistakes

### Mistake 1: Violating the `ownKeys` non-configurable invariant
If `target` has a non-configurable own property, the `ownKeys` trap **must** include that property in the returned array!

```javascript
const target = {};
Object.defineProperty(target, "id", { value: 101, configurable: false });

const proxy = new Proxy(target, {
  ownKeys() {
    return ["name"]; // Omitted non-configurable key 'id'!
  }
});

// CRASH! TypeError: 'ownKeys' on proxy: trap result did not include non-configurable key 'id'
Object.keys(proxy); 
```

### Mistake 2: Attaching an `apply` trap to a plain non-function object
Defining an `apply` or `construct` trap on a proxy where `target` is a plain `{}` object throws a `TypeError` when instantiated.

---

## Best Practices
- **Forward unhandled operations to `Reflect` equivalents** (`Reflect.has()`, `Reflect.deleteProperty()`, `Reflect.ownKeys()`, `Reflect.apply()`).
- **Sanitize logged test objects** using `ownKeys` to hide passwords and API tokens from console test logs.
- **Keep trap execution lightweight** to prevent performance bottlenecks during mass object iterations.

---

## Comparison Table

| Trap Name | Intercepted Operation / API | Return Requirement | Common SDET / Framework Use Case |
| :--- | :--- | :--- | :--- |
| **`has`** | `"key" in proxy` | Boolean (`true`/`false`) | Hiding sensitive fields from presence checks |
| **`deleteProperty`**| `delete proxy.key` | Boolean (`true`/`false`) | Protecting test config & fixture parameters |
| **`ownKeys`** | `Object.keys()`, `Reflect.ownKeys()` | Array / Iterable of keys | Filtering secret keys out of logs/reports |
| **`apply`** | `proxyFn(...args)` | Any Function Result | Spy wrappers, performance timers, API mocks |
| **`construct`** | `new ProxyClass(...args)` | Object Instance | Dependency Injection, Instance tracking |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates advanced Proxy traps (has, deleteProperty, ownKeys, apply)
 * building a Secure Automation Configuration Manager with Function Spying.
 */
"use strict";

// 1. Raw Sensitive Configuration Object
const rawConfig = {
  baseURL: "https://api.example.com",
  timeout: 30000,
  apiKey: "secret_live_key_99812",
  _internalCache: { active: true }
};

// 2. Advanced Security Handler
const secureConfigHandler = {
  // Intercept 'in' operator checks
  has(target, prop) {
    if (prop === "apiKey" || prop.startsWith("_")) {
      return false; // Hides secret keys from presence checks!
    }
    return Reflect.has(target, prop);
  },

  // Intercept deletion attempts
  deleteProperty(target, prop) {
    if (prop === "baseURL" || prop === "timeout") {
      console.log(`[SECURITY] Blocked deletion attempt on critical key: '${prop}'`);
      return false; // Rejects deletion
    }
    return Reflect.deleteProperty(target, prop);
  },

  // Intercept Object.keys() / Key enumeration APIs
  ownKeys(target) {
    // Filter out internal underscore keys and secret API keys
    return Reflect.ownKeys(target).filter(
      key => typeof key === "symbol" || (!key.startsWith("_") && key !== "apiKey")
    );
  }
};

const configProxy = new Proxy(rawConfig, secureConfigHandler);

// Verification: 'has' trap hiding fields
console.log("'baseURL' in proxy:", "baseURL" in configProxy); // Expected: true
console.log("'apiKey' in proxy:", "apiKey" in configProxy);   // Expected: false (Hidden!)

// Verification: 'ownKeys' trap filtering directory outputs
console.log("Exposed Keys (Object.keys):", Object.keys(configProxy)); 
// Expected Output: ["baseURL", "timeout"] (apiKey & _internalCache removed!)

// Verification: 'deleteProperty' blocking deletion in strict mode
try {
  delete configProxy.baseURL;
} catch (err) {
  console.log("Delete Guard Error:", err.message);
  // Expected Output: 'deleteProperty' on proxy: trap returned falsish for property 'baseURL'
}

// 3. Function Spying via 'apply' Trap Demonstration
function calculateDiscount(price, percent) {
  return price - (price * (percent / 100));
}

let callCount = 0;
const spyCalculate = new Proxy(calculateDiscount, {
  apply(targetFn, thisArg, argsList) {
    callCount++;
    console.log(`[SPY] Invoked calculateDiscount #${callCount} with args:`, argsList);
    return Reflect.apply(targetFn, thisArg, argsList);
  }
});

console.log("Result 1:", spyCalculate(100, 20)); // Expected: 80
console.log("Result 2:", spyCalculate(200, 10)); // Expected: 180
console.log("Total Execution Count:", callCount); // Expected: 2
```

---

## Key Takeaways
- Advanced Proxy traps (`has`, `deleteProperty`, `ownKeys`, `apply`) allow 360-degree control over object presence, deletion, key iteration, and function execution.
- The `has` trap intercepts `"key" in proxy` and must return a boolean.
- The `deleteProperty` trap intercepts `delete proxy.key` and must return a boolean.
- The `ownKeys` trap customizes `Object.keys()` output, but must include all non-configurable own target keys.
- The `apply` trap intercepts function execution, powering spy wrappers and mock objects.

---

## Related
- [[js-proxy-api-foundations]] — Foundations of `get` and `set` proxy traps.
- [[js-reflect-api]] — Standardized `Reflect` methods matching all Proxy traps.
- [[js-object-property-existence-checks]] — Comparing `in` vs `Object.hasOwn()`.
- [[MOC - JS Advanced Mechanics & Metaprogramming]] — Central Map of Content for Metaprogramming.
