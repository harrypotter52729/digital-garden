---
id: 202606120834
title: JSON.parse & JSON.stringify
aliases:
  - json-methods
  - serialization
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/objects
date_created: 2026-06-12
mastery_level: 1
---

# JSON.parse & JSON.stringify

> **TL;DR:** **Serialization** (`JSON.stringify`) is like **freeze-drying** a physical house model into a flat packet of powder for shipping. **Parsing** (`JSON.parse`) is like **adding water to reconstitute it** back into its original 3D shape.

## The Mental Model
In memory, JavaScript [[MOC - JS Objects & Structures|objects]] are complex spiderwebs of [[js-primitive-vs-reference-types|references]]. You cannot directly transmit a live reference across the network or save it to a plain text file. 

To send data, you must translate it into a simple flat sequence of text characters (JSON format). 
- `JSON.stringify` flattens the object into text.
- `JSON.parse` inflates the text back into a living object structure in memory.

---

## 1. Flattening Data: `JSON.stringify`
When you stringify an object, JavaScript has rules for what it keeps and what it throws away:

> [!WARNING]
> **Data Loss Quirks:** 
> - Functions, Symbols, and `undefined` values are **discarded** when serializing objects.
> - If they are elements of an array, they are converted to `null`.
> - `NaN` and `Infinity` are converted to `null`.
> - Dates are converted to UTC ISO-strings.

- **Circular References:** If object `A` references object `B`, and `B` references `A`, trying to stringify `A` will throw a fatal `TypeError: Converting circular structure to JSON` (infinite loop error).

### The Extra Arguments
`JSON.stringify(value, replacer, space)`
- **Replacer:** An array or function to filter/modify properties before conversion.
- **Space:** A number or string used to insert white space (indentation) for pretty-printing.

---

## 2. Inflating Data: `JSON.parse`
Takes a JSON-compliant string and converts it back into JS types.

> [!IMPORTANT]
> **Strict JSON Rules:** JSON requires **double quotes** around key names and strings. If you pass single quotes or trailing commas, `JSON.parse()` will throw a `SyntaxError`.

### The Reviver
`JSON.parse(text, reviver)`
The `reviver` function runs on every key-value pair during parsing. This is crucial for reviving date strings back into proper `Date` objects, since JSON has no native Date data type.

---

## Canonical Code Example

Here is a script demonstrating how to serialize objects with custom formatting and safely parse back dates:

```javascript
// --- 1. Stringifying with Quirks & Formatting ---
const user = {
  name: "Hemanth",
  age: undefined,          // Will be discarded
  secret: Symbol("key"),   // Will be discarded
  greet: () => "Hello!",   // Will be discarded
  interests: ["coding", undefined] // undefined becomes null in arrays
};

// Stringify with 2-space formatting indentation
const jsonText = JSON.stringify(user, null, 2);
console.log(jsonText);
/* Output:
{
  "name": "Hemanth",
  "interests": [
    "coding",
    null
  ]
}
*/


// --- 2. Parsing with a Reviver (Date Restoration) ---
const backupText = '{"username":"Hemanth","backupDate":"2026-06-12T03:00:00.000Z"}';

// Without a reviver, backupDate remains a plain string
const plainObj = JSON.parse(backupText);
console.log(typeof plainObj.backupDate); // "string"

// Using a reviver function to automatically turn date strings into Date objects
const hydratedObj = JSON.parse(backupText, (key, value) => {
  if (key === "backupDate") {
    return new Date(value); // Reconstitutes Date object
  }
  return value;
});

console.log(hydratedObj.backupDate instanceof Date); // true
console.log(hydratedObj.backupDate.getFullYear()); // 2026
```

---

## Related
* [[MOC - JS Objects & Structures]] - Core syntax of JavaScript objects.
* [[js-browser-storage-local-session-cookies]] - Storing serialized objects in localStorage.
* [[js-primitive-vs-reference-types]] - Differentiating references from primitive values.
