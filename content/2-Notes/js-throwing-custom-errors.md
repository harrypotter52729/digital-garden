
---
id: 202602081502
title: Throwing Custom Errors
aliases: [throw, Custom Errors, Error Object]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/error-handling
date_created: {{date}}
mastery_level: 1
---
# Throwing Custom Errors

Sometimes your code runs perfectly fine from a JavaScript engine perspective, but fails your specific **business logic** (e.g., a user tries to withdraw more money than they have in their account). In these cases, you need to manually halt execution using the `throw` keyword.

## The `throw` Keyword
When you use `throw`, the current function immediately stops executing, and control is passed to the nearest `catch` block in the call stack. If there is no `catch` block, the program crashes.

## The Built-in `Error` Object
While you *can* throw a primitive value (like `throw "Something went wrong"`), you should **always throw an Error object**. The `Error` object captures the `stack trace` (the exact file and line number where the error occurred), which is critical for debugging.

```javascript
function withdrawMoney(amount, balance) {
  if (amount > balance) {
    // Manually trigger an error
    throw new Error("Insufficient funds"); 
  }
  return balance - amount;
}

try {
  withdrawMoney(100, 50);
} catch (e) {
  console.log(e.message); // "Insufficient funds"
}
```

## Creating Custom Error Classes

For large applications, it is useful to create specific error types by extending the built-in `Error` class. This allows you to handle different errors in different ways.



```JavaScript
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = "ValidationError"; // Identify the specific error type
  }
}

// Inside a catch block later:
if (error instanceof ValidationError) {
  showUserFriendlyAlert(error.message);
} else {
  sendErrorToCrashAnalytics(error);
}
```

## Related
- [[js-try-catch-finally-flow]] - How to catch what you throw.
    
- [[js-es6-classes-under-the-hood]] - Extending built-in objects.
* [[pw-handling-custom-error-reporting]] - Playwright wraps automation failures into custom error objects with full stack traces.
