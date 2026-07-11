---
id: 202606120839
title: Generators & Iterators
aliases:
  - generators
  - iterators
  - yield-keyword
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/advanced
date_created: 2026-06-12
mastery_level: 1
---

# Generators & Iterators

> **TL;DR:** A generator is like a **video game checkpoint system** for functions. Instead of running a function from start to finish all at once, you can pause execution at a checkpoint (`yield`), walk away, and resume it exactly where you left off later by calling `.next()`.

## 1. The Iterator Protocol
Before looking at generators, we must understand **Iterators** (which govern [[js-for-of-iterator|for...of loops]]). An Iterator is an object that lets you traverse a collection one item at a time.

Any object is an iterator if it has a `.next()` method that returns:
- `{ value: any, done: boolean }`

When `done` becomes `true`, the loop knows there are no more items left.

---

## 2. Generator Functions (`function*` and `yield`)
Normal functions in JavaScript obey the "Run-to-Completion" rule—once they start executing, nothing can pause them until they return.

Generators break this rule. They are marked with an asterisk (`function*`) and use the `yield` keyword to pause themselves.

### How it works:
1. Calling a generator function does **not** run any code. Instead, it returns a **Generator Object**.
2. Calling `generator.next()` runs the code until it hits the first `yield` statement.
3. The function pauses, freezes its entire state (variables, scope, stack), and returns the yielded value: `{ value: yieldedVal, done: false }`.
4. The next call to `.next()` defreezes the function and resumes running immediately *after* that last `yield`.

---

## 3. Advanced Power: Two-Way Communication
`yield` is not just an exit door; it is also an entry door. You can pass a value *back* into the generator by passing an argument to `.next(value)`. 

The value you pass to `.next()` becomes the evaluated result of the `yield` expression inside the generator!

---

## Canonical Code Example

Here is a script showing a basic generator, a two-way communication flow, and a lazy infinite ID generator:

```javascript
// --- 1. Basic Generator Loop ---
function* countSteps() {
  console.log("Starting...");
  yield "Step 1 complete";
  console.log("Resuming...");
  yield "Step 2 complete";
  return "All done!";
}

const steps = countSteps(); // Returns the generator iterator object

console.log(steps.next()); // Starting... { value: 'Step 1 complete', done: false }
console.log(steps.next()); // Resuming... { value: 'Step 2 complete', done: false }
console.log(steps.next()); // { value: 'All done!', done: true }


// --- 2. Infinite Data Stream (On-Demand) ---
// Since it executes lazily, this infinite loop won't crash the browser!
function* uniqueIdGenerator() {
  let id = 1;
  while (true) {
    yield `id_${id}`;
    id++;
  }
}

const ids = uniqueIdGenerator();
console.log(ids.next().value); // "id_1"
console.log(ids.next().value); // "id_2"
console.log(ids.next().value); // "id_3"


// --- 3. Two-Way Communication ---
function* conversationalist() {
  // 1. Pause and send out the question.
  // 2. When next(answer) is called, the response replaces the entire yield statement.
  const name = yield "What is your name?";
  console.log(`Hello, ${name}!`);
  
  const hobby = yield "What do you like to do?";
  console.log(`Wow, ${hobby} sounds super fun!`);
}

const chat = conversationalist();
// Start the generator (must do this without arguments first to reach the first yield)
console.log(chat.next().value); // Prints: "What is your name?"

// Send the name back in
console.log(chat.next("Hemanth").value); // Logs: "Hello, Hemanth!", Prints: "What do you like to do?"

// Send the hobby back in
chat.next("Coding"); // Logs: "Wow, Coding sounds super fun!"
```

---

## Related
* [[js-for-of-iterator]] - Loop syntax designed for iterables.
* [[js-iterable-vs-enumerable]] - Iteration protocols vs enlisting properties.
* [[js-the-event-loop-and-call-stack]] - Execution context stacks during yielding.
