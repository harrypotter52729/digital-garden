---
id: 202606131222
title: Browser Context & Page Model
aliases:
  - browser-context
  - playwright-isolation
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/architecture
date_created: 2026-06-13
mastery_level: 1
---

# Browser Context & Page Model

> **TL;DR:** A **Browser Context** is like a **fresh Incognito window**. It provides absolute isolation for cookies, cache, and localStorage, letting you run parallel tests on a single browser instance without them polluting or logging into each other's sessions.

## The Browser Hierarchy

Playwright organizes browser execution into three distinct layers:

```
  [ Browser ] (The core application in memory - e.g. Chromium)
       |
  +----+----+ (Under 100ms context initialization)
  |         |
[Context 1] [Context 2] (Independent sessions - like Incognito windows)
  |         |
[Page]    [Page] (Individual tabs/windows within that session)
```

### 1. Browser
- **What it is:** The actual executable application running in your operating system (e.g. Chromium, Firefox, WebKit).
- **Why it matters:** Spinning up a browser takes significant CPU and memory. Playwright launches the browser **only once** per worker to save resources.

### 2. Browser Context
- **What it is:** An isolated, lightweight session profile running inside the browser.
- **Why it exists:** Traditional test runners (like Selenium) had to close and relaunch the entire browser between tests to clear state, which took seconds. Playwright contexts are created in **milliseconds** and share the same browser executable while maintaining complete cookie, cache, and session storage isolation.

### 3. Page
- **What it is:** A single browser tab or pop-up window within a specific Browser Context.
- **Rules:** Pages inside the same Context **share** cookies and local storage, while pages in different Contexts are completely blind to one another.

---

## E2E Testing Isolation Best Practice
In a standard Playwright test suite, you don't manually create browsers and contexts. The test runner handles this automatically using **fixtures**:

1. A worker starts a single **Browser** instance.
2. For each test, Playwright creates a fresh **Browser Context**.
3. It hands you a new **Page** tab inside that context.
4. When the test completes, the context is destroyed, instantly clearing all cookies and storage.

This prevents **test pollution**—where Test A logs in or adds a product to a cart, and Test B fails because it inherits that residual state.

---

## Canonical Code Example

If you are writing a custom script (outside the test runner), here is how you build the hierarchy programmatically:

```javascript
const { chromium } = require("@playwright/test");

async function runIsolatedSessions() {
  // 1. Launch the browser (expensive operation)
  const browser = await chromium.launch({ headless: false });

  // 2. Create Context A (User 1 - clean slate)
  const contextA = await browser.newContext();
  const pageA = await contextA.newPage();
  await pageA.goto("https://automationexercise.com/login");
  
  // Perform actions that set cookies/sessions...
  await pageA.fill('[data-qa="login-email"]', "userA@test.com");
  
  // 3. Create Context B (User 2 - completely isolated from Context A)
  // This spins up in milliseconds and has zero cookies or storage from User A
  const contextB = await browser.newContext();
  const pageB = await contextB.newPage();
  await pageB.goto("https://automationexercise.com/login");
  
  // Both users can operate simultaneously without session conflicts
  console.log("Both contexts running independently!");

  // 4. Teardown
  await contextA.close(); // Clears User A's session state
  await contextB.close(); // Clears User B's session state
  await browser.close();  // Shuts down the browser application
}

runIsolatedSessions();
```

---

## Related
* [[js-async-await-syntax]] - How Playwright coordinates asynchronous browser control.
* [[js-promises-anatomy-and-chaining]] - The underlying JavaScript Promises returned by launch and context APIs.
* [[pw-playwright-config-file]] - Configuring default worker contexts and viewport dimensions globally.
* [[js-browser-storage-local-session-cookies]] - Browser contexts isolate cookies and localStorage to prevent state pollution.
* [[js-garbage-collection-mark-and-sweep]] - Closing contexts discards references, allowing garbage collection to free browser memory.
