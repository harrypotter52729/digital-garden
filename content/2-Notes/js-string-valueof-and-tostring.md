---
id: 202607201938
title: "String Object Conversion: valueOf and toString"
aliases:
  - valueof-and-tostring
  - string-autoboxing-valueOf
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
  - topic/type-conversion
date_created: 2026-07-20
mastery_level: 1
---

# String Object Conversion: valueOf and toString

> **TL;DR:** **valueOf()** extracts the **raw primitive candy from inside a wrapper** (unwraps a `String` object back into a primitive string). **toString()** returns a **string description of the object** (for `String` objects, this is identical to the raw text, but other objects decide how to describe themselves).

## Context: Primitive Strings vs. String Objects
In JavaScript, there is a key distinction between primitive strings and wrapper objects:

```javascript
const primitive = "Hello";         // typeof "string" (Fast, preferred)
const wrapper = new String("Hello"); // typeof "object" (Slow, avoid)
```

### 1. Autoboxing
Primitives do not have methods. When you run a method on a primitive string (e.g. `primitive.toUpperCase()`), JavaScript temporarily wraps it in a `String` object (**autoboxing**), calls the method, and immediately destroys the temporary object.

### 2. The Dangers of `new String()`
You should **never** declare strings using `new String()` because it introduces bugs:
* **Strict Equality Fails:** `new String("Java") === "Java"` returns `false` because they are different types (object vs string).
* **Reference Comparisons:** Comparing two String objects `new String("Java") === new String("Java")` returns `false` because JavaScript compares objects by memory reference, not by content.

---

## 1. `valueOf()`
Returns the primitive string value of a `String` object. It explicitly extracts the primitive string from the wrapper object.
* Internally, the engine retrieves the hidden `[[PrimitiveValue]]` slot of the object.

---

## 2. `toString()`
Returns a string representation of the object. 
* For `String` objects, `toString()` behaves identically to `valueOf()`, returning the raw primitive string.
* However, other objects override `toString()` to describe themselves:
  * Array: `[1, 2].toString()` $\rightarrow$ `"1,2"`
  * Date: `new Date().toString()` $\rightarrow$ `"Mon Jul 20 2026..."`
  * Plain Object: `({}).toString()` $\rightarrow$ `"[object Object]"`

---

## Implicit Coercion
You rarely need to call `valueOf()` or `toString()` manually. When JavaScript expects a primitive string during string concatenation or loose equality (`==`) checks, it automatically invokes `valueOf()` behind the scenes:

```javascript
const obj = new String("Hello");
console.log(obj + " World"); // Coerced to "Hello World" (engine calls obj.valueOf() implicitly)
```

---

## Canonical Code Example

Here is a copy-pasteable script demonstrating wrapper objects, autoboxing, equality quirks, and type coercion:

```javascript
// --- 1. Primitive vs. Wrapper Types ---
const strObj = new String("JavaScript");

console.log("Type of primitive:", typeof "JavaScript"); // "string"
console.log("Type of wrapper:", typeof strObj);         // "object"

// Extracting primitive value using valueOf and toString
console.log("Unwrapped using valueOf():", strObj.valueOf());       // "JavaScript"
console.log("Unwrapped using toString():", strObj.toString());     // "JavaScript"
console.log("TypeOf valueOf return:", typeof strObj.valueOf());   // "string"


// --- 2. The Equality Trap ---
const p1 = "Hello";
const w1 = new String("Hello");
const w2 = new String("Hello");

console.log("Loose equality (performs coercion):", w1 == p1);   // true
console.log("Strict equality (fails on type):", w1 === p1);     // false
console.log("Object reference compare:", w1 === w2);            // false (different references!)


// --- 3. Autoboxing Mechanics ---
const name = "zoro";
// Autoboxing allows primitive to run methods
const upperName = name.toUpperCase();
console.log("Method called on primitive:", upperName); // "ZORO"


// --- 4. How other objects override toString ---
console.log("Array toString:", [10, 20].toString());      // "10,20"
console.log("Object toString:", { name: "Luffy" }.toString()); // "[object Object]"
```

---

## Related
* [[js-string-concat]] - String concatenation and implicit coercion during joins.
* [[js-string-autoboxing]] - In-depth study on automatic object wrapping.
* [[js-strict-vs-loose-equality]] - Equality operations and coercion rules.
* [[js-primitive-vs-reference-types]] - Comparing mutable references vs immutable values.
* [[MOC - JS Type Conversion & Equality]] - Coercion pathways and conversion rules.
* [[MOC - JS Built-in Objects & Utilities]] - Central directory for standard utility methods.
