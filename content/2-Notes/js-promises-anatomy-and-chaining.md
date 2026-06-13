---
id: 202602081204
title: Promises Anatomy and Chaining
aliases: [Promises]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/async
date_created: {{date}}
mastery_level: 1
---
# Promises Anatomy and Chaining

A **Promise** is an object representing the eventual completion (or failure) of an asynchronous operation and its resulting value. It restores control by allowing you to attach callbacks to a trusted local object rather than passing them into a black-box function.

## The Three States
A Promise is always in one of three mutually exclusive states:
1. **Pending:** The initial state; the operation has not completed yet.
2. **Fulfilled (Resolved):** The operation completed successfully.
3. **Rejected:** The operation failed.

*(Once a promise is fulfilled or rejected, it is considered **Settled** and its state/value can never change).*

## Creating a Promise
```javascript
const myPromise = new Promise((resolve, reject) => {
  let success = true;
  if (success) {
    resolve("Data fetched successfully!"); // Transitions to Fulfilled
  } else {
    reject("Network Error!"); // Transitions to Rejected
  }
});
```

## Consuming and Chaining Promises

Promises solve Callback Hell via chaining. Every `.then()` method returns a _new_ Promise, allowing for flat, vertical chains.

```JavaScript
fetchUserData()
  .then(user => fetchUserPosts(user.id))
  .then(posts => console.log(posts))
  .catch(error => console.error("Caught any error in the chain:", error))
  .finally(() => console.log("Hides loading spinner regardless of success/fail"));
```

## Related
- [[js-callbacks-and-inversion-of-control]] - What Promises were built to fix.
- [[js-microtasks-vs-macrotasks]] - `.then` and `.catch` callbacks are pushed to the Microtask queue.
* [[pw-actionability-and-auto-waiting]] - Playwright's auto-waiting loops rely on resolving JavaScript promises under the hood.
* [[pw-web-first-assertions]] - Web-first assertions return promises that poll the DOM until resolved or timed out.
