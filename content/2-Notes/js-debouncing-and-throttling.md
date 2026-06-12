---
id: 202606120831
title: Debouncing & Throttling
aliases:
  - debouncing
  - throttling
  - performance-patterns
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/performance
date_created: 2026-06-12
mastery_level: 1
---

# Debouncing & Throttling

> **TL;DR:** Both patterns rate-limit function calls. **Debouncing** is like an **elevator door** that waits for a pause in passengers before closing. **Throttling** is like a **drip irrigation system** that releases water strictly once every 10 seconds, regardless of how hard it rains.

## The Mental Model
High-frequency events like typing, scrolling, or window resizing can trigger a JavaScript function hundreds of times per second. This can lag your UI.

- **Debouncing:** "Wait until the user has *stopped* doing the action for `X` milliseconds before executing the function."
  - *Example:* A search box that fetches suggestions. You only want to query the API when the user stops typing, not on every single keypress.
- **Throttling:** "Run the function immediately, but then refuse to run it again until `X` milliseconds have passed."
  - *Example:* A window scroll listener tracking page position. You want to run the layout calculations at a steady pace (e.g., once every 100ms) rather than 60 times a second.

---

## Direct Comparison

| Feature | Debounce | Throttle |
| :--- | :--- | :--- |
| **Trigger Time** | After a period of inactivity. | At regular intervals during activity. |
| **Execution** | Executes *once* at the end of the burst. | Executes *repeatedly* at a controlled rate. |
| **Ideal For** | Search bars, auto-saves, forms validation. | Infinite scrolls, resize checks, mouse trails. |

---

## How They Work Under the Hood (Closures)
Both patterns utilize [[js-closures-and-lexical-environment|closures]] to keep track of a timer/cooldown state between function invocations.

---

## Canonical Code Example

Here is a full implementation of both utility functions:

```javascript
// --- 1. Debounce Implementation ---
function debounce(callback, delay) {
  let timerId; // Closed-over variable to hold the timer reference
  
  return function (...args) {
    // Clear any active timer, resetting the countdown
    clearTimeout(timerId);
    
    // Set a new timer to run the callback after the delay
    timerId = setTimeout(() => {
      callback.apply(this, args);
    }, delay);
  };
}

// --- 2. Throttle Implementation ---
function throttle(callback, limit) {
  let inThrottle = false; // Closed-over flag to lock execution
  
  return function (...args) {
    if (!inThrottle) {
      // Execute the callback immediately
      callback.apply(this, args);
      // Lock execution
      inThrottle = true;
      
      // Unlock after the limit duration expires
      setTimeout(() => {
        inThrottle = false;
      }, limit);
    }
  };
}

// --- Usage Simulation ---

// Setup a debounced handler for typing
const handleSearchInput = debounce((searchTerm) => {
  console.log(`API Request made for: "${searchTerm}"`);
}, 300);

// Setup a throttled handler for scrolling
const handleWindowScroll = throttle(() => {
  console.log(`Updated scroll position: ${window.scrollY}px`);
}, 500);

// Simulator checks (Uncomment to test in a browser/node environment):
// handleSearchInput("a"); // Timer starts
// handleSearchInput("ab"); // Timer resets, starts again
// handleSearchInput("abc"); // Timer resets, executes after 300ms
```

---

## Related
* [[js-closures-and-lexical-environment]] - Retaining timer reference variables in memory.
* [[js-the-event-loop-and-call-stack]] - Scheduling timers via queue APIs.
* [[js-higher-order-functions-and-callbacks]] - Wrapping and executing rate-limited callbacks.
