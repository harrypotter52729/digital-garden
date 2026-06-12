---
id: 202602081205
title: Async Await Syntax
aliases: [async/await]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/async
date_created: {{date}}
mastery_level: 1
---
# Async / Await Syntax

Introduced in ES8 (2017), `async/await` is syntactic sugar on top of Promises. It allows you to write asynchronous, Promise-based code that *looks* and *reads* like synchronous code.

## The Rules
1. **`async` keyword:** Placing `async` before a function guarantees that the function will return a Promise. If you return a primitive value, JS automatically wraps it in a resolved Promise.
2. **`await` keyword:** Can only be used inside an `async` function. It pauses the execution of *that specific function* until the Promise settles, while the rest of the JS Engine (Event Loop) continues running other code.

## The Transformation
**Using standard Promises (`.then`):**
```javascript
function getUserData() {
  fetch('/api/user')
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error(error));
}
```

**Using `async/await`:**

```JavaScript
async function getUserData() {
  try {
    const response = await fetch('/api/user'); // Pauses execution here until fetch completes
    const data = await response.json();        // Pauses execution here until json is parsed
    console.log(data);
  } catch (error) {
    console.error(error); // Replaces .catch()
  }
}
```

## Important Pitfall: Sequential vs Concurrent

If tasks don't depend on each other, do not `await` them sequentially, as it creates a bottleneck. Instead, start them simultaneously and use `Promise.all()`.

## Related

- [[js-promises-anatomy-and-chaining]] - The underlying technology of async/await.
- [[js-the-event-loop-and-call-stack]] - How `await` yields control back to the call stack.
- [[js-try-catch-finally-flow]] _(Future Note)_ - Required for handling errors in async/await.