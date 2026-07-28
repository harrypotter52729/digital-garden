---
id: 202607280840
title: The Deep Merge Algorithm
aliases:
  - deep merge
  - object deep merge
  - recursive merge
  - deep merge javascript
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/algorithms
  - topic/objects
date_created: 2026-07-28
mastery_level: 1
---

# The Deep Merge Algorithm

> **TL;DR:** `Object.assign(target, source)` or spread `{ ...target, ...source }` is a shallow bucket swap—if both objects contain an `address` folder, the source's `address` folder completely overwrites and throws away the target's `address` folder. A **Deep Merge** recursively steps inside nested folders, combining individual properties line-by-line without discarding pre-existing nested data!

---

## Why This Exists
In software configuration management, API test automation, and Redux/state initialization, developers need to combine default settings with custom user overrides (e.g. merging `{ db: { host: "localhost", port: 5432 } }` with user overrides `{ db: { port: 9999 } }`).

Shallow merging via `Object.assign()` or `{ ...defaults, ...overrides }` completely overwrites nested object references (`db` becomes `{ port: 9999 }`, losing `host: "localhost"`). The **Deep Merge Algorithm** recursively traverses nested object graphs, preserving non-conflicting nested properties while updating specified overrides.

---

## Mental Model
Imagine merging two structured document folders:
- **Shallow Merge (`Object.assign`):** If Folder B has a tab named "Financials", you throw away Folder A's entire "Financials" section and replace it with Folder B's section.
- **Deep Merge:** You open Folder A's "Financials" section and Folder B's "Financials" section side-by-side. Page-by-page, you keep Folder A's pages, update pages that Folder B explicitly modified, and add any new pages Folder B introduced.

```
SHALLOW MERGE OVERWRITE (Object.assign)
Target:  { db: { host: "localhost", port: 5432 } }
Source:  { db: { port: 9999 } }
Result:  { db: { port: 9999 } }  ──► LOST `host` PROPERTY!

DEEP MERGE RECURSION (deepMerge)
Target:  { db: { host: "localhost", port: 5432 } }
Source:  { db: { port: 9999 } }
Result:  { db: { host: "localhost", port: 9999 } }  ──► PRESERVED `host` & UPDATED `port`!
```

---

## How It Works

1. **Input Parameters:** Accepts a `target` object and a `source` object.
2. **Key Traversal:** Uses `Reflect.ownKeys(source)` to iterate through all own keys (including Symbol keys) on `source`.
3. **Type Evaluation per Key:**
   - If `source[key]` AND `target[key]` are both plain objects: Recursively call `deepMerge(target[key], source[key])`.
   - If `source[key]` is an object but `target[key]` is primitive/undefined: Instantiate a fresh object copy and recurse.
   - If `source[key]` is a primitive or array: Assign `source[key]` directly to `target[key]` (overwriting target value).
4. **Return Value:** Returns the mutated or cloned merged target object.

---

## Key Characteristics
- **Nested Preservation:** Preserves non-overlapping nested attributes during object merging.
- **Array Handling Decisions:** Arrays can be treated as atomic replacements (`sourceArray` replaces `targetArray`) or concatenated, depending on business requirements.
- **Prototype Protection:** Must sanitize inputs to block prototype pollution keys (`__proto__`, `constructor`, `prototype`).

---

## Common Mistakes

### Mistake 1: Relying on Object spread `{ ...a, ...b }` for nested configurations
Developers expect `{ ...defaultConfig, ...userConfig }` to merge nested settings.

```javascript
const defaultConfig = { server: { host: "localhost", port: 8080, ssl: true } };
const userConfig = { server: { port: 3000 } };

// WRONG: Shallow spread overwrites 'server' object entirely!
const merged = { ...defaultConfig, ...userConfig };

console.log(merged.server.ssl); // Output: undefined! (ssl & host data lost!)
```

### Mistake 2: Prototype Pollution Vulnerability during deep merge
Failing to block `__proto__` or `constructor` keys during dynamic deep merging allows malicious payloads to pollute `Object.prototype`!

```javascript
// VULNERABLE CODE without key sanitization:
function unsafeDeepMerge(target, source) {
  for (let key in source) {
    // Dangerous if key === "__proto__"!
    target[key] = deepMerge(target[key], source[key]); 
  }
}
```

---

## Best Practices
- **Sanitize keys against Prototype Pollution** (`key === "__proto__"` or `key === "constructor"`) when writing deep merge utilities.
- **Treat arrays as atomic replacements** by default unless explicitly concatenating lists.
- **Use established deep merge libraries** (like Lodash `_.merge`) for edge cases involving Dates, Maps, and Sets in production systems.

---

## Comparison Table

| Attribute / Feature | Shallow Merge (`Object.assign`) | Deep Merge (`deepMerge`) |
| :--- | :--- | :--- |
| **Top-Level Property Overwrite**| ✅ Overwrites target key | ✅ Overwrites target key |
| **Nested Object Overwrite** | ❌ **Replaces entire object reference**| ✅ **Recursively merges nested keys** |
| **Preserves Unchanged Nested Keys**| ❌ No (Lost) | ✅ **Yes (Preserved)** |
| **Symbol Keys Support** | Yes | Yes (via `Reflect.ownKeys`) |
| **Performance Overhead** | `O(N)` (Top-level keys) | `O(N)` (Total nodes in object tree) |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates a production-grade, prototype-pollution-safe Deep Merge algorithm
 * handling nested configurations, Symbol keys, and array strategy decisions.
 */

// 1. Safe Deep Merge Implementation
function deepMerge(target, source) {
  // Ensure target is a valid object
  if (!target || typeof target !== "object") target = {};
  if (!source || typeof source !== "object") return target;

  // Extract all own keys (Strings and Symbols)
  const keys = Reflect.ownKeys(source);

  for (const key of keys) {
    // PROTOTYPE POLLUTION GUARD
    if (key === "__proto__" || key === "constructor" || key === "prototype") {
      continue; // Skip dangerous keys
    }

    const sourceVal = source[key];
    const targetVal = target[key];

    // Helper: Check if value is a plain non-array Object
    const isObject = val => val && typeof val === "object" && !Array.isArray(val) && !(val instanceof Date);

    if (isObject(sourceVal) && isObject(targetVal)) {
      // Both are objects -> Recurse deeply
      target[key] = deepMerge({ ...targetVal }, sourceVal);
    } else if (isObject(sourceVal)) {
      // Source is object, target is not -> Clone source object deeply
      target[key] = deepMerge({}, sourceVal);
    } else {
      // Primitives, Arrays, or Dates -> Replace target value directly
      target[key] = sourceVal;
    }
  }

  return target;
}

// 2. Testing Deep Merge on Enterprise Configurations
const defaultAppConfig = {
  appName: "VaultAutomation",
  environment: "production",
  database: {
    host: "10.0.0.1",
    port: 5432,
    credentials: { user: "admin", maxPool: 20 }
  },
  features: ["auth", "logging"]
};

const customTestOverrides = {
  environment: "staging",
  database: {
    port: 9999, // Override port only
    credentials: { user: "test_user" } // Override user, keep maxPool
  },
  features: ["auth", "logging", "mocking"] // Array replaced
};

// 3. Executing Deep Merge
const finalConfig = deepMerge(defaultAppConfig, customTestOverrides);

console.log("Merged Environment:", finalConfig.environment); // Expected: "staging"
console.log("Preserved DB Host:", finalConfig.database.host);     // Expected: "10.0.0.1" (Preserved!)
console.log("Updated DB Port:", finalConfig.database.port);       // Expected: 9999 (Updated!)
console.log("Preserved maxPool:", finalConfig.database.credentials.maxPool); // Expected: 20 (Preserved!)
console.log("Updated DB User:", finalConfig.database.credentials.user);     // Expected: "test_user"
```

---

## Key Takeaways
- Shallow merging (`Object.assign`, `{ ...spread }`) replaces nested object references, causing nested property data loss.
- Deep merging steps recursively into nested objects, merging non-conflicting properties line-by-line.
- Always implement guards against Prototype Pollution (`__proto__`, `constructor`) when parsing dynamic merge sources.
- Use `Reflect.ownKeys()` during deep merge traversal to ensure Symbol properties are preserved.

---

## Related
- [[js-object-assign-vs-spread]] — Shallow copying mechanics and tradeoffs.
- [[js-structured-clone]] — Native deep cloning vs deep merging.
- [[js-property-descriptors]] — Inspecting own properties and descriptors.
- [[MOC - JS Objects & Structures]] — Central Map of Content for Objects.
