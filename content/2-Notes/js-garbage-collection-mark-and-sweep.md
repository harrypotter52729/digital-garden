---
id: 202606120842
title: Garbage Collection - Mark & Sweep
aliases:
  - garbage-collection
  - memory-management
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/advanced
date_created: 2026-06-12
mastery_level: 1
---

# Garbage Collection: Mark & Sweep

> **TL;DR:** Garbage collection is like a **condo inspector** who visits your house. They start at the main entrance (the root) and tag every item connected to you (the **Mark** phase). Then, they sweep through the house and throw away everything left untagged (the **Sweep** phase) to free up physical space.

## The Mental Model: Reachability
JavaScript manages memory automatically. When you create variables, arrays, or objects, the engine allocates memory in the Heap (see [[MOC - JS Data Types & Memory]]). When you don't need them anymore, the engine cleans them up.

But how does the engine know you are done? The golden rule is **Reachability**.
- A value is "reachable" if it can be accessed or used somehow from a set of starting values called **Roots** (such as the global variable window, local parameters in the active [[js-the-event-loop-and-call-stack|call stack]], etc.).
- If a value cannot be reached from any root, it is considered garbage, and its memory is reclaimed.

---

## 1. The Mark & Sweep Algorithm
This is the standard algorithm used by modern JS engines (V8, JavaScriptCore, SpiderMonkey). It runs in two phases:

### Phase 1: Mark
The garbage collector starts at the **Roots** and descends along their references. Every time it visits a referenced object, it tags it as "marked" (reachable).

### Phase 2: Sweep
The collector sweeps through the Heap memory. Any object that does **not** have a mark tag is deleted, returning its memory back to the operating system. Finally, it clears all the mark tags to prepare for the next garbage collection cycle.

```
 [Root] ---> [Object A (Marked)] ---> [Object B (Marked)]
 
             [Object C (Unmarked)] ---> [Object D (Unmarked)]
```
*Result: Objects C and D are swept away because there is no link from Root to reach them.*

---

## 2. Why Older Algorithms Failed (Reference Counting)
Older engines used **Reference Counting** (counting how many variables reference an object). If the count reached 0, the object was deleted.

This failed during a **Circular Reference**:
```javascript
function makeCycle() {
  const obj1 = {};
  const obj2 = {};
  obj1.a = obj2; // obj2 count = 1
  obj2.a = obj1; // obj1 count = 1
}
makeCycle();
```
Even though the function completes and `obj1` and `obj2` are out of scope (unreachable), their reference counts remain at `1`, causing a **memory leak** under reference counting. 

**Mark & Sweep** solves this because once the function exits, they cannot be reached from the root, so they are marked for cleanup.

---

## Canonical Code Example

Here is a script demonstrating reachability, circular links, and a common memory leak vector (forgotten intervals):

```javascript
// --- 1. Memory Cleanup Simulation ---
let user = {
  name: "Hemanth",
  profile: {
    avatar: "avatar.jpg"
  }
};

// Object is currently reachable from root: global variable 'user' -> 'profile' -> 'avatar'

// Breaking the link
user.profile = null;
// The avatar object { avatar: "avatar.jpg" } is now unreachable.
// Mark & Sweep will destroy it on the next cycle.


// --- 2. Circular References (Safely Cleared by Mark & Sweep) ---
function createCycle() {
  const elementA = {};
  const elementB = {};
  
  elementA.friend = elementB;
  elementB.friend = elementA;
  
  return "Done";
}

createCycle();
// Once createCycle finishes, elementA and elementB are out of scope.
// Because they are unreachable from roots, the garbage collector sweeps them away.


// --- 3. The Memory Leak Vector: Forgotten Interval ---
function leakMemory() {
  const bigData = new Array(1000000).fill("data");
  
  setInterval(() => {
    // This closure captures 'bigData' and keeps it reachable.
    // As long as the interval is running, bigData CANNOT be garbage collected!
    console.log("Interval running...");
  }, 1000);
}

// leakMemory(); // Running this causes a permanent memory leak!
```

---

## Related
* [[MOC - JS Data Types & Memory]] - Heap allocation and reference memory models.
* [[js-closures-and-lexical-environment]] - Closures preventing garbage collection.
* [[js-the-event-loop-and-call-stack]] - Active call stack reachability roots.
* [[pw-browser-context-and-page-model]] - Clearing contexts drops references so the engine can run mark-and-sweep sweeps.
