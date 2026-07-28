---
id: 202607280828
title: JSON Serialization & Parsing (stringify & parse)
aliases:
  - JSON serialization
  - JSON parsing
  - JSON.stringify
  - JSON.parse
  - replacer and reviver
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/json
  - topic/serialization
date_created: 2026-07-28
mastery_level: 1
---

# JSON Serialization & Parsing (`stringify` & `parse`)

> **TL;DR:** Sending a JavaScript object across the network is like sending a live dog through the postal service—the post office can't ship a living creature in a letter envelope. `JSON.stringify()` converts the dog into a flat blueprint description text (a JSON string) that fits in an envelope. Upon arrival, `JSON.parse()` reads the blueprint text and reconstructs the data object!

---

## Why This Exists
JavaScript objects exist as dynamic memory pointer graphs in RAM. Different backend systems (Java, Python, C#, Go, SQL databases) cannot read JavaScript RAM memory structures directly over an HTTP network connection.

**JSON (JavaScript Object Notation)** provides a language-independent, text-based data interchange format. `JSON.stringify()` serializes in-memory JS objects into standard JSON text strings, while `JSON.parse()` deserializes JSON text strings back into JavaScript objects.

---

## Mental Model
Think of JSON processing as a factory translation pipeline:
- **`JSON.stringify(obj, replacer, space)`**: Takes an in-memory object graph, applies a **`replacer` filter function** to strip or sanitize sensitive keys, and formats the output string with optional **`space` indentation**.
- **`JSON.parse(text, reviver)`**: Takes a raw JSON text string, parses the syntax, and passes every key-value pair through a **`reviver` transformation function** (e.g., converting ISO date strings back into `Date` objects) before building the final object.

```
OBJECT IN RAM                        NETWORK INTERCHANGE                    OBJECT IN RAM
┌───────────────────┐                ┌──────────────────────┐               ┌───────────────────┐
│ {                 │ JSON.stringify │ {"name":"Luffy",     │  JSON.parse   │ {                 │
│   name: "Luffy",  ├───────────────►│  "created":          ├──────────────►│   name: "Luffy",  │
│   created: Date   │                │  "2026-01-01T..."}   │ (with reviver)│   created: Date   │
│ }                 │                └──────────────────────┘               │ }                 │
└───────────────────┘                                                       └───────────────────┘
```

---

## How It Works

### 1. `JSON.stringify(value, [replacer], [space])`
- **Value Handling Rules:**
  - Objects, Arrays, Strings, Numbers, Booleans, `null` are serialized.
  - `Date` objects are automatically converted to ISO strings via `Date.prototype.toJSON()`.
  - `undefined`, Functions, and Symbols inside objects are **omitted entirely**.
  - `undefined`, Functions, and Symbols inside arrays are converted to **`null`** to preserve array indices.
  - **Circular References:** Throws a `TypeError: Converting circular structure to JSON`.
- **`replacer` Parameter:** Can be an Array of allowed key strings or a filtering function `(key, value) => newValue`. Returning `undefined` skips the key.
- **`space` Parameter:** Controls pretty-printing whitespace indentation (e.g., `2` for 2-space formatting).

### 2. `JSON.parse(text, [reviver])`
- Parses valid JSON formatted text strings. Throws a `SyntaxError` if syntax quotes or commas are invalid.
- **`reviver` Parameter:** A transformation function `(key, value) => transformedValue` executed on every node (bottom-up traversal).

---

## Key Characteristics
- **Strict Syntax Rules:** JSON requires **double quotes** (`"key": "value"`) around property names and strings. Single quotes or unquoted keys throw `SyntaxError`.
- **Data Loss Hazard:** `JSON.stringify()` drops functions, Symbols, `undefined`, `Map`, `Set`, `RegExp`, and `TypedArray` data.

---

## Common Mistakes

### Mistake 1: Using `JSON.parse(JSON.stringify(obj))` for deep cloning complex data
Relying on JSON stringification for deep cloning corrupts `Date` objects (converts to strings), drops `Map`/`Set` data, omits functions, and crashes on circular references!

```javascript
const original = {
  created: new Date(),
  map: new Map(`["a", 1]`)
};

const clone = JSON.parse(JSON.stringify(original));

console.log(typeof clone.created); // Output: "string" (LOST Date instance!)
console.log(clone.map);            // Output: {} (LOST Map data entirely!)

// FIX: Use structuredClone(original) instead!
```

### Mistake 2: Using single quotes inside raw JSON strings
JSON specification strictly mandates double quotes for keys and string values.

```javascript
// WRONG: Throws SyntaxError: Unexpected token ' in JSON
JSON.parse("{'name': 'Luffy'}"); 

// CORRECT:
JSON.parse('{"name": "Luffy"}');
```

---

## Best Practices
- **Use the `replacer` parameter** to scrub sensitive fields (passwords, auth tokens) before logging payloads in API test runs.
- **Use the `reviver` parameter** with `JSON.parse()` to automatically restore ISO date strings into native `Date` objects.
- **Use `structuredClone()`** for deep cloning objects in memory rather than `JSON.parse(JSON.stringify())`.

---

## Comparison Table

| Attribute / Type | JavaScript Object | JSON Text Format | Behavior in `JSON.stringify()` |
| :--- | :--- | :--- | :--- |
| **Data Format** | In-Memory Object Graph | Plain Text String | Target Output |
| **Key Quotes** | Unquoted / Optional | **Strict Double Quotes (`"key"`)**| Forced to double quotes |
| **Functions** | Supported | **Not Supported** | **Omitted from objects / `null` in arrays** |
| **`undefined`** | Supported | **Not Supported** | **Omitted from objects / `null` in arrays** |
| **`Date` Objects** | `Date` Instance | ISO 8601 String | Converted via `.toJSON()` ISO string |
| **Circular Refs** | Supported | **Not Supported** | **Throws TypeError** |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates JSON.stringify with replacer filtering & pretty printing,
 * JSON.parse with a reviver date transformer, and circular reference traps.
 */

// 1. Target Object with Various Types & Sensitive Fields
const apiPayload = {
  id: 101,
  username: "monk_luffy",
  password: "super_secret_password_123",
  userRole: undefined,          // Will be omitted
  action: function() {},        // Will be omitted
  createdAt: new Date("2026-01-01T12:00:00.000Z"),
  tags: ["captain", undefined, "emperor"] // Array undefined becomes null
};

// 2. JSON.stringify with Replacer (Sanitizing Sensitive Fields & Pretty Printing)
const sanitizedJSON = JSON.stringify(apiPayload, (key, value) => {
  // Filter out password field during serialization
  if (key === "password") {
    return undefined; // Strips key from JSON output
  }
  return value;
}, 2); // 2-space indentation

console.log("Sanitized Pretty JSON:\n", sanitizedJSON);
/* Expected Output:
{
  "id": 101,
  "username": "monk_luffy",
  "createdAt": "2026-01-01T12:00:00.000Z",
  "tags": [
    "captain",
    null,
    "emperor"
  ]
}
*/

// 3. JSON.parse with Reviver (Restoring ISO Date Strings to Date Objects)
const rawServerResponse = '{"id":202,"name":"Zoro","joined":"2026-05-15T08:30:00.000Z"}';

const parsedResponse = JSON.parse(rawServerResponse, (key, value) => {
  // Date ISO String Detector Regex
  const isoDateRegex = /^\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}\.\d{3}Z$/;
  
  if (typeof value === "string" && isoDateRegex.test(value)) {
    return new Date(value); // Transform string back to Date instance!
  }
  return value;
});

console.log("Parsed User:", parsedResponse.name); // Expected: "Zoro"
console.log("Joined Date Instance Check:", parsedResponse.joined instanceof Date); // Expected: true
console.log("Parsed Year:", parsedResponse.joined.getUTCFullYear()); // Expected: 2026

// 4. Circular Reference Exception Trap
const circularObj = { name: "Loop" };
circularObj.self = circularObj;

try {
  JSON.stringify(circularObj);
} catch (err) {
  console.log("Caught Circular Error:", err.message); 
  // Expected Output: Converting circular structure to JSON
}
```

---

## Key Takeaways
- `JSON.stringify()` serializes objects into JSON text; `JSON.parse()` deserializes JSON text into objects.
- Functions, `undefined`, and Symbols are omitted from serialized objects and converted to `null` in arrays.
- Use `replacer` functions with `JSON.stringify()` to filter sensitive fields (passwords, tokens).
- Use `reviver` functions with `JSON.parse()` to transform strings (e.g. ISO dates) back into native objects.
- Do not use `JSON.parse(JSON.stringify())` for deep cloning; use `structuredClone()` instead.

---

## Related
- [[js-structured-clone]] — Native deep cloning without JSON data loss pitfalls.
- [[js-objects-vs-primitives]] — Data types and string conversion fundamentals.
- [[js-map-vs-object]] — JSON compatibility tradeoffs between Map and Object.
- [[MOC - JS Built-in Objects & Utilities]] — Central Map of Content for Utilities.
