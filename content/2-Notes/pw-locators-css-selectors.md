---
id: 202608152008
title: Playwright Locators and CSS Selector Construction Rules
aliases:
  - locators
  - css selectors
  - page.locator
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Playwright Locators and CSS Selector Construction Rules

---

> **TL;DR:** `page.locator()` is an engine that dynamically targets web elements on demand—it doesn't store static DOM elements, but acts as a laser sight that locates elements every time an action is executed.

---

## Why This Exists

Legacy tools stored static references to DOM elements (`findElement()`). If the DOM re-rendered or updated asynchronously, static references threw `StaleElementReferenceException`. Playwright locators are lazy evaluators: calling `page.locator(selector)` creates an immutable description of how to find an element, re-evaluating the selector live in the DOM whenever an action is performed.

---

## Mental Model

Think of a locator as an address on a GPS map ("Go to 100 Main Street") rather than a physical house object. If the house is repainted or renovated, the GPS address still points to the correct location. Every time you ask Playwright to act (`click()`, `fill()`), it re-reads the address live on the map.

---

## How It Works

1. **Locator Creation:** `const element = page.locator(selector);` creates a locator object without touching the DOM. No `await` is required here!
2. **Lazy Evaluation:** The DOM search occurs only when an action method (`click()`, `fill()`, `textContext()`) is called on the locator.
3. **Strict Mode Enforcement:** By default, Playwright locators enforce strict mode—if a selector matches multiple elements when a single-element action (like `click()`) is called, Playwright throws a `strict mode violation` error.

---

## CSS Selector Construction Rules

| Element Attribute | CSS Selector Pattern | Syntax Rule | Example |
| :--- | :--- | :--- | :--- |
| **ID Attribute** | `#id` | `#` + ID value | `#username` |
| **Class Attribute** | `.class` | `.` + Class name | `.form-control` |
| **Generic Attribute** | `[attr="val"]` | `[attribute="value"]` | `[name="email"]` |
| **Tag + Attribute** | `tag[attr="val"]` | Tag + bracketed attribute | `input[type="password"]` |
| **Parent to Child** | `parent child` | Space between selectors | `.card-body h4` |

---

## Key Characteristics

- **Lazy & Resilient:** Eliminates `StaleElementReferenceException` errors entirely.
- **Strict Mode by Default:** Fails fast if a selector matches multiple elements when a single action is requested.
- **Chaining Locators:** Combine parent and child locators (`page.locator('.card').locator('.btn')`).

---

## Common Mistakes

- **Awaiting locator creation:** Writing `await page.locator('#btn')`. Locators are constructed synchronously; `await` belongs strictly on the action method call (`await page.locator('#btn').click()`).
- **Ignoring strict mode violations:** Using non-unique CSS selectors that match 4 elements on the page for single-element actions without scoping or indexing.

---

## Best Practices

- Prefer user-facing role locators (`page.getByRole('button')`) or test IDs (`page.getByTestId('submit')`) when possible.
- Use explicit CSS attribute combinations (`input[name="username"]`) when test IDs are unavailable.

---

## Canonical Code Example

```javascript
// Demonstrating Playwright locator construction rules and lazy evaluation
const { test, expect } = require('@playwright/test');

test('demonstrate locator rules', async ({ page }) => {
  await page.goto('https://example.com/login');

  // 1. Locator declaration is synchronous (NO await here!)
  const usernameInput = page.locator('#username');                // ID rule (#)
  const passwordInput = page.locator('input[type="password"]');  // Attribute rule
  const submitButton  = page.locator('button.btn-primary');        // Tag + Class rule

  // 2. Action method execution (Requires await!)
  await usernameInput.fill('testuser');
  await passwordInput.fill('secret123');
  await submitButton.click();

  // 3. Parent-Child Chaining
  const container = page.locator('.form-container');
  const errorMsg  = container.locator('.alert-danger');
  await expect(errorMsg).toBeVisible();
});
```

---

## Key Takeaways

- `page.locator()` creates lazy, reusable descriptions of how to find DOM elements.
- Locator creation is synchronous; action methods are asynchronous (`await`).
- Enforces strict mode to prevent accidental interactions with ambiguous elements.

---

## Related

- [[pw-multiple-element-locators]] — Handling multi-element selectors
- [[pw-element-actions-fill-vs-type]] — Performing actions on locators
- [[MOC - Playwright Locators and Actions]]
