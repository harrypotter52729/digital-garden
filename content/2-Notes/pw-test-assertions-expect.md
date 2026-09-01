---
id: 202608152006
title: Playwright Test Assertions and expect Engine
aliases:
  - expect assertions
  - playwright assertions
  - web-first assertions
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Playwright Test Assertions and expect Engine

---

> **TL;DR:** `expect()` in Playwright is like an automated quality inspector standing next to your conveyor belt—it continuously re-evaluates web elements until they match expected conditions or throws a detailed failure report when the timer runs out.

---

## Why This Exists

Traditional testing assertion libraries (like Chai or Jest plain equality assertions) inspect data values statically at a single instant in time. In modern dynamic web applications, DOM elements change state asynchronously. Playwright provides built-in **Web-First Assertions** via the `expect` object, which automatically re-evaluate assertions until conditions pass or timeouts expire.

---

## Mental Model

Imagine attempting to verify that a store door is unlocked.
- **Generic Assertion (`assert(door.isUnlocked === true)`):** Checks the door latch once at microsecond zero. If the clerk is in the middle of turning the key, the check fails instantly.
- **Web-First Assertion (`await expect(door).toBeUnlocked()`):** Stands at the door and periodically tests the handle over a 5-second window. The moment the clerk finishes turning the key, the assertion passes cleanly.

---

## How It Works

1. **Importing expect:** `expect` is imported directly from `@playwright/test`. No external third-party assertion library (like Chai or Jasmine) is required.
2. **Web-First vs Generic Assertions:**
   - **Web-First (Async):** Targets locators and pages (e.g., `await expect(locator).toBeVisible()`). Continuously retries DOM checks until the `expect` timeout (default 5,000ms) expires.
   - **Generic (Sync):** Targets static JavaScript primitive values or objects (e.g., `expect(value).toBe(42)`). Evaluates once synchronously without retrying.

---

## Key Characteristics

- **Built-in Auto-Retrying:** Web-first assertions automatically wait for asynchronous DOM state changes.
- **Dedicated Assertion Timeout:** Governed independently by `expect: { timeout: 5000 }` in `playwright.config.js`.
- **Rich Failure Reporting:** Emits clear, color-coded terminal outputs detailing expected vs actual values and DOM snapshots upon failure.

---

## Common Mistakes

- **Forgetting `await` on Web-First assertions:** Writing `expect(locator).toBeVisible()` without `await`. The assertion won't wait for DOM retries, resulting in race conditions.
- **Importing `expect` from third-party libraries:** Accidentally importing `expect` from Jest or Chai instead of `@playwright/test`.

---

## Best Practices

- Always use `await` on locator and page assertions (e.g., `await expect(locator).toHaveText('Success')`).
- Adjust assertion timeouts globally in `playwright.config.js` rather than littering code with per-assertion inline timeout overrides.

---

## Canonical Code Example

```javascript
// Demonstrating Playwright Web-First Assertions vs Generic Assertions
const { test, expect } = require('@playwright/test');

test('demonstrate web-first and generic assertions', async ({ page }) => {
  await page.goto('https://example.com/login');

  // 1. Web-First Page Assertion (Async - Auto-Retrying)
  await expect(page).toHaveTitle('Example Domain');

  // 2. Web-First Locator Assertion (Async - Auto-Retrying)
  const heading = page.locator('h1');
  await expect(heading).toBeVisible();
  await expect(heading).toHaveText('Example Domain');

  // 3. Generic Value Assertion (Sync - Single Evaluation)
  const currentUrl = page.url();
  expect(currentUrl).toContain('example.com');
});
```

---

## Key Takeaways

- `expect` is built natively into `@playwright/test`.
- Web-first assertions targeting locators/pages are asynchronous and auto-retry until the expect timeout.
- Always prefix web-first assertions with `await`.

---

## Related

- [[pw-test-annotation-structure]] — Spec file layout
- [[pw-async-await-execution]] — Placement of await in assertions
- [[MOC - Playwright Assertions]]
