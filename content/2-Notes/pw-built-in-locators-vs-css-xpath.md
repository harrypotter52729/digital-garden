---
id: 202606131226
title: Built-in Locators vs CSS & XPath
aliases:
  - playwright-locators
  - get-by-role
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/locating-interacting
date_created: 2026-06-13
mastery_level: 1
---

# Built-in Locators vs CSS & XPath

> **TL;DR:** Playwright recommends using **built-in locators** (like `getByRole` or `getByText`) that find elements based on how a user sees and interacts with the page (accessibility semantics). Avoid deep CSS paths and custom XPaths, which make tests brittle and prone to breaking whenever the visual design or markup structure changes.

## The Department Store Analogy

Imagine you are looking for help in a department store:

| Selector Strategy | Description | Analogy | Reliability |
| :--- | :--- | :--- | :--- |
| **CSS/XPath** | `page.locator('div > ul > li:nth-child(3) > a')` | "Find the person sitting at coordinates X, Y behind desk 4." | **Very Low** (If desk 4 is moved or the layout is shifted, you find the wrong person or nothing). |
| **Role-based (Semantic)** | `page.getByRole('button', { name: 'Submit' })` | "Find the cashier standing at the cash register." | **High** (Even if they shift registers or rearrange the store layout, they are still a cashier). |

By selecting elements by their **semantic role** rather than their **code structure**, your tests naturally mirror how humans interact with web pages. If a button's location changes but it remains a button with the text "Submit", your test stays green.

---

## Playwright's Recommended Locators

Playwright provides a set of highly optimized, built-in locators:

1. **`getByRole(role, options)`**: Locates elements by their ARIA accessibility attributes (e.g., `button`, `heading`, `checkbox`, `link`). *This is the gold standard.*
2. **`getByLabel(text)`**: Locates form input fields by matching their associated `<label>` text.
3. **`getByPlaceholder(text)`**: Locates inputs by matching their placeholder text attribute.
4. **`getByText(text)`**: Finds elements containing specific visible text.
5. **`getByTestId(id)`**: Fallback locator for elements with a dedicated testing attribute (e.g., `data-testid="submit-btn"`), configured in your `playwright.config.js`.

---

## Refactoring Brittle Selectors

Here is a side-by-side comparison showing how structural selectors compare to accessible, resilient locators:

| Brittle Selector (CSS/XPath) | Resilient Locator (Playwright Built-in) |
| :--- | :--- |
| `page.locator('.form-btn-submit')` | `page.getByRole('button', { name: 'Submit' })` |
| `page.locator('//input[@id="username"]')` | `page.getByLabel('Username')` |
| `page.locator('input[placeholder="Search..."]')` | `page.getByPlaceholder('Search...')` |
| `page.locator('.header-nav > li > a')` | `page.getByRole('link', { name: 'Home' })` |

---

## Canonical Code Example

This script illustrates writing resilient test assertions using modern locator practices:

```javascript
// @ts-check
const { test, expect } = require("@playwright/test");

test("Registration and form filling flow", async ({ page }) => {
  await page.goto("/register");

  // 1. Fill out inputs using labels & placeholders (simulating actual user view)
  // This matches: <label for="email-input">Email Address</label><input id="email-input">
  await page.getByLabel("Email Address").fill("test_user@example.com");

  // This matches: <input placeholder="Enter password here...">
  await page.getByPlaceholder("Enter password here...").fill("SecurePass123!");

  // 2. Select checkbox by role + accessible name
  // This matches: <input type="checkbox" id="terms"><label>I agree to terms</label>
  await page.getByRole("checkbox", { name: "I agree to terms" }).check();

  // 3. Click submit by role and name (ignores case and matches button role)
  // This matches: <button type="submit">Submit Registration</button>
  await page.getByRole("button", { name: "Submit Registration" }).click();

  // 4. Verify completion text appears
  // This matches: <h2>Welcome to your dashboard!</h2>
  await expect(
    page.getByRole("heading", { name: "Welcome to your dashboard!" })
  ).toBeVisible();

  // 5. Fallback: Locate using dedicated testing attribute when content changes frequently
  // This matches: <div data-testid="user-token-display">...</div>
  const tokenDisplay = page.getByTestId("user-token-display");
  await expect(tokenDisplay).not.toBeEmpty();
});
```

---

## Related
* [[js-dom-tree-and-element-selection]] - Understanding how structural DOM query selectors differ from user-facing accessibility nodes.
* [[pw-actionability-and-auto-waiting]] - How Playwright's locators perform safety checks prior to running actions.
* [[pw-handling-iframes-and-multiple-tabs]] - Strategies for target locating when components are nested within nested frames.
* [[js-dom-tree-and-element-selection]] - Traverses the DOM tree to locate elements using semantic roles instead of strict CSS paths.
