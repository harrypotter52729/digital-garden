---
id: 202606131238
title: Authentication State Reuse
aliases:
  - storage-state
  - playwright-session-reuse
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/framework-design
date_created: 2026-06-13
mastery_level: 1
---

# Authentication State Reuse

> **TL;DR:** Typing credentials into a login form in every test slows down test runs and puts unnecessary stress on authentication servers. Playwright solves this by allowing you to log in **once**, export the session cookies and localStorage to a **JSON file** (`storageState`), and inject that file into all subsequent test contexts to start them pre-logged-in.

## The Office Badge Analogy

Imagine entering a secure office building every day:

```
Without State Reuse (Manual Login every test):
1. Walk up to the receptionist at the front desk.
2. Provide your ID, type in your passcode, and wait for confirmation.
3. Walk inside.
*Repeat this entire process for every single room you enter.* (Highly inefficient).

With Authentication State Reuse (Storage State):
1. On Day 1, you register at the front desk once.
2. The receptionist hands you a plastic keycard badge (auth.json storage file).
3. For the rest of the week, you skip the desk entirely. 
*You swipe your keycard badge directly at the doors of all rooms.* (Instant entry).
```

In Playwright:
- **The Keycard Badge:** A JSON file containing session cookies and localStorage items.
- **The Swipe:** Injecting the `storageState` path into your test project configurations.

---

## The Authentication Lifecycle

Here is the setup loop for session caching:

```
  [ Global Setup ] 
         |
         v
  1. Login once via UI/API
  2. Capture state: page.context().storageState({ path: 'auth.json' })
         |
         +------------------------------------------------+
         |                                                |
         v                                                v
[ Test Context 1 ]                               [ Test Context 2 ]
Inject 'auth.json'                               Inject 'auth.json'
Starts session active (Pre-logged in)            Starts session active (Pre-logged in)
```

---

## Canonical Code Example

This example details creating a setup test to cache the authentication session and configuring the `playwright.config.js` to distribute that session across tests.

### 1. The Setup Test (`tests/auth.setup.js`)

```javascript
// @ts-check
const { test: setup, expect } = require("@playwright/test");

const authFile = "playwright/.auth/user.json";

setup("authenticate and save state", async ({ page }) => {
  // 1. Perform UI login actions
  await page.goto("/login");
  await page.getByLabel("Username").fill("premium_member");
  await page.getByLabel("Password").fill("Password123!");
  await page.getByRole("button", { name: "Log In" }).click();

  // 2. Wait for login sequence to complete (redirect to dashboard)
  await expect(page.getByRole("heading", { name: "Dashboard" })).toBeVisible();

  // 3. Export session state (cookies & localStorage) to a local JSON file
  await page.context().storageState({ path: authFile });
  console.log("Session saved successfully to:", authFile);
});
```

### 2. The Configuration (`playwright.config.js`)

```javascript
// @ts-check
const { defineConfig, devices } = require("@playwright/test");

module.exports = defineConfig({
  projects: [
    // Project A: Setup project that runs before other tests
    {
      name: "setup",
      testMatch: /auth.setup.js/,
    },
    // Project B: Main testing project
    {
      name: "chromium",
      use: {
        ...devices["Desktop Chrome"],
        // Tell Playwright to inject the saved JSON session state file
        storageState: "playwright/.auth/user.json",
      },
      // Dependency: Run the setup project first
      dependencies: ["setup"],
    },
  ],
});
```

### 3. A Pre-Authenticated Test (`tests/dashboard.spec.js`)

```javascript
// @ts-check
const { test, expect } = require("@playwright/test");

test("User can access dashboard page directly", async ({ page }) => {
  // Since storageState is injected, we navigate directly without logging in!
  await page.goto("/dashboard");
  
  // The user is already logged in, so this element is visible immediately
  await expect(page.getByText("Welcome back, premium_member!")).toBeVisible();
});
```

---

## Related
* [[pw-browser-context-and-page-model]] - Explains how cookies and storage are scoped to separate context structures.
* [[pw-playwright-config-file]] - Defining project dependencies and global setups in the configuration block.
* [[js-browser-storage-local-session-cookies]] - Bypasses logins by injecting stored session cookies and localStorage keys directly.
