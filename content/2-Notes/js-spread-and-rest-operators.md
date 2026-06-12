---
id: 202606120828
title: Spread & Rest Operators
aliases:
  - spread-operator
  - rest-parameter
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/syntax
date_created: 2026-06-12
mastery_level: 1
---

# Spread & Rest Operators

> **TL;DR:** The spread and rest operators both use the exact same three dots (`...`) syntax, but do opposite jobs. **Spread** is like unpacking a suitcase onto a bed (unpacking elements), while **Rest** is like throwing all loose remaining items back into a bag (packing elements).

## The Mental Model
The context determines if the three dots are a **Spread** or a **Rest**:
- **Spread:** You are starting with a single container (like an array or object) and expanding it into individual pieces.
- **Rest:** You are taking separate, individual pieces and gathering them up into a single, organized container.

---

## 1. The Spread Operator (Unpacking)
Spread is used to expand elements of an array or object. It is highly useful for duplicating or merging data without mutating the original.

### In Arrays
```javascript
const fruits = ["apple", "banana"];
const allFruits = [...fruits, "cherry", "date"]; 
// ["apple", "banana", "cherry", "date"]
```

### In Objects
```javascript
const original = { name: "Hemanth", role: "Developer" };
const updated = { ...original, role: "Senior Developer" }; 
// { name: "Hemanth", role: "Senior Developer" }
```
*Note: Spread only performs a [[js-shallow-vs-deep-copy|shallow copy]]. Nested objects will still share the same reference.*

---

## 2. The Rest Parameter (Packing)
Rest is used to collect multiple elements and bundle them into a single array. It "gathers up the rest" of the items.

### In Functions
Before ES6, handling dynamic arguments required using the tricky, non-array `arguments` keyword. Rest allows you to capture any extra parameters as a clean array.
```javascript
function sum(...numbers) {
  // 'numbers' is a real array
  return numbers.reduce((acc, curr) => acc + curr, 0);
}
```

### In [[js-destructuring|Destructuring]]
```javascript
const [first, ...others] = ["Gold", "Silver", "Bronze", "Iron"];
// first = "Gold"
// others = ["Silver", "Bronze", "Iron"]
```

---

## Canonical Code Example

Here is a practical look at copying objects safely with spread and capturing arguments with rest:

```javascript
// --- SPREAD EXAMPLE: Unpacking and Merging ---
const defaultSettings = { theme: "light", notifications: true };
const userSettings = { theme: "dark" };

// Merge settings. userSettings overrides defaultSettings keys.
const finalConfig = { ...defaultSettings, ...userSettings };
console.log(finalConfig); // { theme: "dark", notifications: true }

// Copying arrays without mutating the original
const tasks = ["task A", "task B"];
const tasksCopy = [...tasks];
tasksCopy.push("task C");

console.log("Original:", tasks); // ["task A", "task B"] (unmutated!)
console.log("Copy:", tasksCopy);   // ["task A", "task B", "task C"]


// --- REST EXAMPLE: Gathering Remaining Items ---
// A function that processes an order and collects all item names
function processOrder(orderId, status, ...itemNames) {
  console.log(`Order ID: ${orderId} (${status})`);
  
  // itemNames is a true array of all arguments passed after 'status'
  console.log(`Processing ${itemNames.length} items:`);
  itemNames.forEach(item => console.log(`- ${item}`));
}

processOrder(1048, "Pending", "Laptop", "Mouse", "Keyboard", "HDMI Cable");
```

---

## Related
* [[js-destructuring]] - Unpacking keys and variables.
* [[js-shallow-vs-deep-copy]] - Spread operator reference constraints.
* [[MOC - JS Objects & Structures]] - Hub for object structures.
