---
id: 202608152032
title: Accessible Role Locators and Element Filtering with getByRole and filter
aliases:
  - getByRole
  - filter method
  - aria role locators
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Accessible Role Locators and Element Filtering with getByRole and filter

---

> **TL;DR:** `page.getByRole('button', { name: 'Submit' })` locates interactive elements using their implicit ARIA accessibility roles and visible accessible names—reflecting how screen readers and real human users perceive web elements.

---

## Why This Exists

Testing web applications by querying implementation details (like `<div class="btn-v2-main">`) leads to brittle tests that break during CSS re-skinning or HTML tag refactoring (e.g., swapping a `<button>` tag for an `<a>` link). Playwright provides `getByRole()` to locate elements by their semantic ARIA roles (`button`, `link`, `checkbox`, `heading`) and accessible names.

---

## Mental Model

Imagine a blind user using a screen reader to navigate a web page.
The screen reader doesn't say "Div class primary button submit". It reads out loud: **"Button, Submit"** (`getByRole('button', { name: 'Submit' })`) or **"Heading Level 1, Welcome"** (`getByRole('heading', { level: 1 })`). Writing tests with `getByRole()` ensures your automation suite tests the page exactly as accessibility screen readers interpret it.

---

## How It Works

1. **Role Identification:** `page.getByRole(role, options)`:
   - **`role`:** Semantic ARIA role string (`'button'`, `'link'`, `'checkbox'`, `'heading'`, `'textbox'`, `'dialog'`).
   - **`options.name`:** Accessible name string or RegExp pattern (visible inner text or `aria-label`).
2. **Chained Locator Filtering:** `.filter(options)`:
   - Apply `.filter({ hasText: 'Nokia' })` onto multi-element locators to isolate specific container cards matching text criteria without manual `for` loops!

---

## Common ARIA Role Types

| HTML Tag / ARIA Role | `getByRole()` Syntax | Description |
| :--- | :--- | :--- |
| `<button>`, `<input type="button">` | `getByRole('button', { name: 'Save' })` | Interactive button elements |
| `<a href="...">` | `getByRole('link', { name: 'Shop' })` | Hyperlinks |
| `<input type="checkbox">` | `getByRole('checkbox', { name: 'Agree' })` | Checkboxes |
| `<h1>`, `<h2>`, `<h3>` | `getByRole('heading', { name: 'Dashboard' })` | Headings |
| `<input type="text">` | `getByRole('textbox', { name: 'Email' })` | Text input fields |

---

## Key Characteristics

- **Accessibility First:** Recommended by Playwright as the primary, most resilient locator strategy.
- **Role Name Matching:** Matches exact or partial string regex against accessible names.
- **Seamless Chaining with `.filter()`:** Filter multi-element role results cleanly.

---

## Canonical Code Example

```javascript
// Demonstrating getByRole locators paired with .filter() chaining
const { test, expect } = require('@playwright/test');

test('locate elements using accessible getByRole and filter', async ({ page }) => {
  await page.goto('https://example.com/shop');

  // 1. Locate primary button by ARIA role and accessible name
  await page.getByRole('button', { name: 'Login' }).click();

  // 2. Target hyperlink by role
  await page.getByRole('link', { name: 'Shop' }).click();

  // 3. Chain locator filter onto product cards to find item with text 'Nokia Edge'
  const productCard = page.locator('.card-body')
    .filter({ hasText: 'Nokia Edge' });

  // 4. Click the 'Add to Cart' button INSIDE that filtered card
  await productCard.getByRole('button', { name: 'Add to Cart' }).click();

  // Verify assertion
  await expect(page.getByRole('link', { name: 'Cart' })).toContainText('1');
});
```

---

## Key Takeaways

- `getByRole()` is Playwright's recommended primary locator strategy.
- Locates elements by ARIA semantic role (`button`, `link`, `heading`) and accessible name.
- Pair with `.filter({ hasText: '...' })` to scope card container selections cleanly.

---

## Related

- [[pw-user-facing-locators-get-by-label]] — `getByLabel()` form locators
- [[pw-refactoring-accessibility-locators]] — Refactoring tests to accessibility locators
- [[MOC - Playwright Locators and Actions]]
