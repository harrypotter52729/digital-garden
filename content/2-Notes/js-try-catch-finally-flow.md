---
id: 202602081501
title: Try, Catch, and Finally Flow
aliases: [try/catch, Error Handling]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/error-handling
date_created: {{date}}
mastery_level: 1
---
# Try, Catch, and Finally Flow

When JavaScript encounters a runtime error, it normally "throws" an exception and completely halts the execution of the script. The `try...catch` statement allows you to handle these errors gracefully without crashing the application.

## The Execution Flow
1. **`try` block:** The JS engine attempts to execute the code inside this block. If no errors occur, the `catch` block is ignored.
2. **`catch` block:** If an error occurs in the `try` block, execution immediately stops and jumps to the `catch` block. It automatically receives an `Error` object containing details about what went wrong.
3. **`finally` block (Optional):** Code inside this block will **always execute**, regardless of whether an error was thrown or not. It is typically used for cleanup operations (like closing a database connection or hiding a loading spinner).

```javascript
isLoading = true;

try {
  // Attempt a risky operation
  const data = JSON.parse("{ badly formatted json }"); 
  console.log("This line will never run");
} catch (error) {
  // Handle the failure
  console.error("Failed to parse data:", error.message);
  console.log(error.stack); // Shows where the error originated
} finally {
  // Cleanup
  isLoading = false; 
  console.log("Cleanup complete.");
}
```

## Important Caveats

- **Parse-time Errors:** `try...catch` only works for _runtime_ errors (valid JS code that fails upon execution). It cannot catch syntax errors (like missing curly braces).
    
- **Asynchronous Code:** A standard `try...catch` cannot catch errors inside a `setTimeout` or standard Promise chain (which use `.catch()`). However, it **does** work perfectly with `async/await`.
    

## Related

- [[js-async-await-syntax]] - How `try...catch` replaces `.catch()` in modern async code.
    
- [[js-throwing-custom-errors]] - How to manually trigger the `catch` block.