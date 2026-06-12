---
id: 202602081202
title: Microtasks vs Macrotasks
aliases: [Microtask Queue, Macrotask Queue, Task Queue]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/async
date_created: {{date}}
mastery_level: 1
---
# Microtasks vs Macrotasks

The Event Loop actually manages *two* separate queues for asynchronous callbacks: the **Macrotask Queue** (often just called the Task Queue) and the **Microtask Queue**. 

## The Priority Rule
**The Microtask Queue has absolute priority over the Macrotask Queue.** After every single operation in the Call Stack finishes, the Event Loop checks the Microtask Queue and empties it *completely* before looking at the Macrotask Queue. If a microtask adds another microtask, it will also be executed before any macrotasks.

## Classification
* **Macrotasks:** `setTimeout()`, `setInterval()`, `setImmediate()` (Node), UI rendering, I/O.
* **Microtasks:** `Promises` (`.then()`, `.catch()`, `.finally()`), `queueMicrotask()`, `MutationObserver`.

## The Execution Order Puzzle
```javascript
console.log("1. Sync execution");

setTimeout(() => console.log("2. Macrotask (setTimeout)"), 0);

Promise.resolve().then(() => console.log("3. Microtask (Promise)"));

console.log("4. Sync execution end");

// Output Order:
// 1. Sync execution
// 4. Sync execution end
// 3. Microtask (Promise)
// 2. Macrotask (setTimeout)
```

## Related

- [[js-the-event-loop-and-call-stack]] - The broader context of the JS runtime.
- [[js-promises-anatomy-and-chaining]] - The most common source of microtasks.