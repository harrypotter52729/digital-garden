---
id: 202602081201
title: The Event Loop and Call Stack
aliases: [Event Loop, Call Stack, JS Concurrency Model]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/async
date_created: {{date}}
mastery_level: 1
---
# The Event Loop and Call Stack

JavaScript is a **single-threaded, non-blocking, asynchronous, concurrent** language. It has one Call Stack, meaning it can only execute one piece of code at a time. The Event Loop is the secret to how it handles long-running tasks without freezing the browser.

## The Architecture
1. **The Call Stack:** Where synchronous code executes. It operates on a Last-In-First-Out (LIFO) basis.
2. **Web APIs (or C++ APIs in Node):** Provided by the environment (browser/Node), not the JS Engine itself. Examples include `setTimeout`, DOM events, and `fetch`. They handle the background work.
3. **The Callback Queue (Task Queue):** When a Web API finishes its task in the background, it pushes the associated callback function into this queue.
4. **The Event Loop:** A continuous process that checks two things:
   - *Is the Call Stack empty?*
   - *Is there a callback waiting in the Callback Queue?*
   - If both are true, it pushes the first item from the queue onto the Call Stack to be executed.

## The Classic Example
```javascript
console.log("1. Start");

setTimeout(() => {
  console.log("2. Timeout Callback");
}, 0);

console.log("3. End");

// Output:
// 1. Start
// 3. End
// 2. Timeout Callback
```

Even with a delay of `0` milliseconds, `setTimeout` is handed off to the Web API. The callback is placed in the queue and must wait for the synchronous code ("1" and "3") to finish clearing the Call Stack before the Event Loop moves it over.

## Related

- [[js-microtasks-vs-macrotasks]] - Nuances in how different queues are prioritized.