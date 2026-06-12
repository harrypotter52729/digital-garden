---
id: 202602081203
title: Callbacks and Inversion of Control
aliases: [Callback Hell, Inversion of Control]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/async
date_created: {{date}}
mastery_level: 1
---
# Callbacks and Inversion of Control

Before Promises and Async/Await, passing a callback function was the only way to handle asynchronous tasks in JavaScript. 

## The Two Major Problems with Callbacks

### 1. Callback Hell (The Pyramid of Doom)
When asynchronous tasks depend on each other, callbacks must be nested inside callbacks. This leads to code that grows horizontally, becoming incredibly difficult to read and maintain.

### 2. Inversion of Control (The Trust Issue)
When you pass a callback to a third-party library or an API (like an analytics tracker or a payment gateway), you hand over control of *how* and *when* that function is executed. 

```javascript
// You write this:
thirdPartyCheckoutAPI(cartData, function chargeCreditCard() {
  // Charge the user $100
});
```

## The Risks:

- What if the third-party API has a bug and calls your callback twice? The user gets charged twice.

- What if it never calls the callback at all?

- What if it calls it synchronously instead of asynchronously, breaking your application state?

This "Inversion of Control" was the primary driver for the creation of Promises, which un-invert the control by giving you back an object that you observe.

## Related
[[js-higher-order-functions-and-callbacks]] - The mechanics of passing functions as arguments.
[[js-promises-anatomy-and-chaining]] - How Promises solve inversion of control.