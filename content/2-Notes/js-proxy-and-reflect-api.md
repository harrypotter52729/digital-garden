---
id: 202606120840
title: Proxy & Reflect API
aliases:
  - proxy-api
  - reflect-api
  - reactivity
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/advanced
date_created: 2026-06-12
mastery_level: 1
---

# Proxy & Reflect API

> **TL;DR:** A **Proxy** is like a **personal assistant** standing in front of an object. Every time someone tries to talk to the object (get or set properties), the assistant intercepts the message and can block, log, validation-check, or modify the response using **Reflect**.

## The Mental Model
Normally, objects in JavaScript are open books: you read and write properties with zero restrictions. 

By wrapping an object in a `Proxy`, you place a gatekeeper in front of it. The gatekeeper has a list of **traps** (like intercepting clicks on a door). When someone does `obj.key`, the trap fires first, running custom JavaScript.

---

## 1. Core Terms
- **Target:** The original, raw object being wrapped (the person the assistant is protecting).
- **Handler:** An object containing the intercepting functions (the "traps").
- **Traps:** Specific methods inside the handler. Common ones:
  - `get(target, property, receiver)`: Triggers when reading a property.
  - `set(target, property, value, receiver)`: Triggers when writing a property.
  - `has(target, property)`: Triggers when checking `property in object`.

---

## 2. Why Reflect?
Inside a trap, you often need to forward the request to the original target. You could do this manually: `target[property] = value`. 

However, doing it manually is prone to errors, especially regarding inheritance ([[js-the-this-keyword-rules|`this` binding]]). 
**Reflect** is a companion object containing static methods matching every proxy trap. Calling `Reflect.set(target, property, value, receiver)` forwards the action to the target safely, returning `true` on success and handling context bindings automatically.

---

## 3. Real-World Use Case: Reactivity (Vue 3)
Modern frameworks like Vue track when a variable is changed to automatically re-render the page. They do this by wrapping state in a Proxy:
- When a property is read in the HTML, the `get` trap runs and logs: *"This component needs to watch this key."*
- When the property is updated, the `set` trap runs and triggers: *"Re-render the HTML now!"*

---

## Canonical Code Example

Here is a script showing how to build a validating and logging Proxy using the Reflect API:

```javascript
const user = {
  name: "Hemanth",
  age: 30
};

// Define the gatekeeper rules (the Handler)
const handler = {
  // 1. Intercept read operations
  get(target, prop, receiver) {
    console.log(`[Read Audit] Property "${prop}" was accessed.`);
    
    // Check if property exists
    if (!(prop in target)) {
      return `Error: Property "${prop}" does not exist.`;
    }
    
    // Safely forward the operation using Reflect
    return Reflect.get(target, prop, receiver);
  },

  // 2. Intercept write operations with validation logic
  set(target, prop, value, receiver) {
    console.log(`[Write Audit] Attempting to set "${prop}" to:`, value);
    
    // Validation check: age must be a positive integer
    if (prop === "age") {
      if (typeof value !== "number" || value < 0) {
        throw new TypeError("Age must be a positive number!");
      }
    }
    
    // Safely perform the write operation using Reflect
    // Reflect.set returns true if the write was successful
    return Reflect.set(target, prop, value, receiver);
  }
};

// Wrap our raw user object in the proxy
const userProxy = new Proxy(user, handler);

// Test reading values
console.log(userProxy.name); // Prints audit trace, returns "Hemanth"
console.log(userProxy.address); // Prints audit trace, returns error message

// Test writing values
userProxy.age = 31; // Audit trace prints, value updates successfully
console.log("Current age:", userProxy.age);

// Test invalid write validation
try {
  userProxy.age = -5; // Throws validation TypeError!
} catch (err) {
  console.error("Blocked write:", err.message);
}
```

---

## Related
* [[MOC - JS Objects & Structures]] - Object descriptors intercepted by Proxy.
* [[js-the-this-keyword-rules]] - Context binding using target receivers.
* [[concept-js-iterable-vs-enumerable]] - Iteration traps intercepting property descriptors.
