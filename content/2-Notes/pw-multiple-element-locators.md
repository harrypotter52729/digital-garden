---
id: 202608152011
title: Handling Multiple Matching Elements with first(), last(), and nth()
aliases:
  - strict mode violation
  - multiple elements
  - locator indexing
  - first last nth
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Handling Multiple Matching Elements with first(), last(), and nth()

---

> **TL;DR:** When a CSS selector matches multiple elements on a web page, Playwright throws a strict mode violation error—locator modifiers (`first()`, `last()`, `nth()`) act as target filters that isolate the exact element you want by its index.

---

## Why This Exists

Playwright operates under **Strict Mode** by default for all single-element action methods (`click()`, `fill()`, `textContent()`). If a locator resolves to 4 product cards on an e-commerce page, calling `await page.locator('.product-card').click()` fails with a `strict mode violation` error to prevent unintended clicks. Playwright provides explicit indexing methods (`first()`, `last()`, `nth()`) to resolve selector ambiguity safely.

---

## Mental Model

Imagine walking into a classroom and shouting, "Student, come to the board!" If 20 students are present, everyone looks confused and no one moves (`strict mode violation`). However, if you say, "First student on the left, come to the board!" (`first()`) or "3rd student in row 2!" (`nth(2)`), exactly one student responds cleanly.

---

## How It Works

1. **Selector Ambiguity Identification:** A broad selector like `page.locator('.card-title')` matches an array of elements in the DOM tree.
2. **Index Filtering:**
   - `.first()`: Filters array down to index `0` (the first matching element in DOM order).
   - `.last()`: Filters array down to the last matching element in DOM order.
   - `.nth(index)`: Filters array down to a zero-based index (e.g., `.nth(0)` for 1st element, `.nth(1)` for 2nd element).
3. **Action Execution:** Once filtered to a single element, action methods (`click()`, `textContent()`) execute without strict mode violations.

---

## Key Characteristics

- **Zero-Based Indexing:** `.nth(0)` equals `.first()`; `.nth(1)` targets the 2nd element.
- **Lazy Evaluation Preserved:** Indexing chained locators maintains lazy evaluation mechanics.
- **Strict Mode Protection:** Guarantees deterministic action targeting across list arrays.

---

## Common Mistakes

- **Assuming `.nth(1)` selects the first item:** `.nth()` uses 0-based indexing! `.nth(1)` targets the **second** item.
- **Using 1-based CSS nth-child selectors incorrectly:** Mixing up JavaScript 0-based `.nth(0)` with CSS 1-based `:nth-child(1)`.

---

## Indexing Method Reference

| Method | Target Element | Array Index Equivalent |
| :--- | :--- | :--- |
| `locator.first()` | 1st matching DOM element | `nth(0)` |
| `locator.last()` | Final matching DOM element | `nth(length - 1)` |
| `locator.nth(0)` | 1st matching DOM element | `first()` |
| `locator.nth(1)` | 2nd matching DOM element | Index `1` |
| `locator.nth(2)` | 3rd matching DOM element | Index `2` |

---

## Canonical Code Example

```javascript
// Handling multiple matching elements in Playwright
const { test, expect } = require('@playwright/test');

test('target specific products from a multi-element list', async ({ page }) => {
  await page.goto('https://example.com/shop');

  // Locator matching ALL product title elements on the page (matches 4 items)
  const productTitles = page.locator('.card-body h4');

  // 1. Target First Product using .first()
  const firstProductText = await productTitles.first().textContent();
  console.log(`First Product: ${firstProductText}`); // Expected: iPhone X

  // 2. Target Second Product using zero-based .nth(1)
  const secondProductText = await productTitles.nth(1).textContent();
  console.log(`Second Product: ${secondProductText}`); // Expected: Samsung Note 8

  // 3. Target Final Product using .last()
  const lastProductText = await productTitles.last().textContent();
  console.log(`Last Product: ${lastProductText}`);

  // 4. Click add to cart button on 3rd product card
  const productCards = page.locator('.card-body');
  await productCards.nth(2).locator('button:has-text("Add to Cart")').click();
});
```

---

## Key Takeaways

- Playwright enforces strict mode, throwing errors if a single-element action targets an ambiguous selector.
- Use `.first()`, `.last()`, or `.nth(index)` to filter multi-element locators to a single item.
- `.nth()` uses 0-based indexing (`0` is first, `1` is second).

---

## Related

- [[pw-locators-css-selectors]] — Creating base selectors
- [[pw-all-text-contents-and-auto-wait-limits]] — Extracting lists of text
- [[MOC - Playwright Locators and Actions]]
