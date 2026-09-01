---
id: 202608152002
title: Playwright Browser, BrowserContext, and Page Object Hierarchy
aliases:
  - browser context hierarchy
  - browser vs context vs page
  - playwright object model
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/architecture
date_created: 2026-08-15
mastery_level: 1
---

# Playwright Browser, BrowserContext, and Page Object Hierarchy

---

> **TL;DR:** Think of a `Browser` as a physical laptop building, a `BrowserContext` as an isolated Incognito window session inside that laptop, and a `Page` as a single tab inside that Incognito window.

---

## Why This Exists

Traditional automation tools tied entire browser process launches to single test executions, making multi-user testing (e.g., testing chat apps between User A and User B) extremely slow and resource-heavy. Playwright solves this by introducing a 3-tier object hierarchy (`Browser` $\rightarrow$ `BrowserContext` $\rightarrow$ `Page`), enabling multi-context testing within a single browser process.

---

## Mental Model

```text
┌──────────────────────────────────────────────────────────┐
│ Browser (Single Operating System Binary Process)         │
│                                                          │
│  ┌─────────────────────────┐  ┌───────────────────────┐  │
│  │ BrowserContext A        │  │ BrowserContext B      │  │
│  │ (Cookies / Storage A)   │  │ (Cookies / Storage B) │  │
│  │  ┌───────┐  ┌───────┐   │  │  ┌───────┐            │  │
│  │  │Page A1│  │Page A2│   │  │  │Page B1│            │  │
│  │  └───────┘  └───────┘   │  │  └───────┘            │  │
│  └─────────────────────────┘  └───────────────────────┘  │
└──────────────────────────────────────────────────────────┘
```

- **Browser:** The overhead-heavy application executable (Chromium/Firefox/WebKit process).
- **BrowserContext:** Lightweight, isolated incognito profile instance (zero shared cookies or storage).
- **Page:** Individual tab or popup window within a specific context.

---

## How It Works

1. **`browser = await chromium.launch()`:** Spawns a single browser executable process. Expensive to create (~500ms).
2. **`context = await browser.newContext()`:** Creates an isolated session profile. Fast to create (~10ms). Carries distinct cookies, local storage, permissions, and viewport settings.
3. **`page = await context.newPage()`:** Opens a single web page tab inside the target context for user interactions (`goto`, `click`, `fill`).

---

## Key Characteristics

- **Zero Session Leakage:** `BrowserContext` instances are strictly isolated from each other.
- **Multi-User Real-Time Testing:** Test real-time collaborative applications (e.g., chat, Google Docs editing) by instantiating two contexts within one test.
- **Automatic Fixture Cleanup:** Playwright's test runner creates and disposes of contexts automatically per test.

---

## Common Mistakes

- **Launching new `Browser` processes for every test:** Calling `chromium.launch()` inside every test case slows down execution by 10x. Reuse the browser instance and spawn fresh `BrowserContext` objects instead.
- **Confusing `BrowserContext` with `Page`:** Attempting to call `goto()` on a `BrowserContext` instead of a `Page`.

---

## Best Practices

- Use the default `{ page }` test fixture for standard single-tab tests—Playwright automatically handles context creation and teardown.
- Explicitly instantiate multiple `BrowserContext` instances when testing multi-role interactions (e.g., Admin vs Standard User).

---

## Canonical Code Example

```javascript
// Demonstrating explicit multi-context testing (Multi-User Chat Simulation)
const { chromium } = require('playwright');

(async () => {
  // 1. Launch a single shared Browser engine process
  const browser = await chromium.launch({ headless: true });

  // 2. Create isolated BrowserContext for User A (Alice)
  const contextAlice = await browser.newContext();
  const pageAlice = await contextAlice.newPage();

  // 3. Create isolated BrowserContext for User B (Bob) - Zero shared cookies!
  const contextBob = await browser.newContext();
  const pageBob = await contextBob.newPage();

  // Alice logs in and sends message
  await pageAlice.goto('https://example.com/chat');
  await pageAlice.fill('#message', 'Hello Bob!');
  await pageAlice.click('#send');

  // Bob views chat stream in completely separate session context
  await pageBob.goto('https://example.com/chat');
  
  console.log('Alice and Bob operating in isolated contexts simultaneously.');

  // Clean up resources
  await contextAlice.close();
  await contextBob.close();
  await browser.close();
})();
```

---

## Key Takeaways

- Hierarchy structure: `Browser` $\rightarrow$ `BrowserContext` $\rightarrow$ `Page`.
- Spawning fresh `BrowserContext` objects is fast (~10ms) and guarantees session isolation.
- Enables multi-user real-time test flows cleanly inside a single browser instance.

---

## Related

- [[pw-browser-context-storage]] — Injecting storage states into contexts
- [[pw-test-fixtures-overview]] — Built-in `page` and `context` fixtures
- [[MOC - Playwright Architecture]]
