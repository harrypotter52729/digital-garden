---
id: 202608152024
title: Dynamic Element Iteration and Locator Chaining
aliases:
  - locator chaining
  - dynamic element search
  - scoping locators
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Dynamic Element Iteration and Locator Chaining

---

> **TL;DR:** Locator chaining (`parentLocator.locator(childSelector)`) restricts Playwright's search scope strictly to the boundaries of a single container element—allowing you to dynamically iterate over a list of product cards and click the "Add to Cart" button inside the matching card without touching other cards.

---

## Why This Exists

On dynamic web pages (e.g., e-commerce product grids), multiple card components contain identical button tags ("Add to Cart", "Delete", "View Details"). If you call `page.locator('button:has-text("Add to Cart")')`, Playwright returns a list of 10 identical buttons. Locator chaining scopes locator searches strictly within a specific container (e.g., product card #2), ensuring actions hit the correct card's button.

---

## Mental Model

Imagine searching for a book in a library.
- **Global Page Search (`page.locator('book')`):** Searching all 50,000 books across all 5 floors simultaneously.
- **Chained Container Search (`shelfLocator.locator('book')`):** Walking over to Shelf #4 (`parentLocator`) and searching strictly among the 10 books sitting on that single shelf.

---

## How It Works

1. **Parent Container Collection:** Locate all container cards using a broad selector:
   `const cards = page.locator('.card-body');`
2. **Iterate & Scope:** Loop through container cards using zero-based indexing (`cards.nth(i)`).
3. **Chained Child Search:** Chain a child selector onto the container locator:
   `const title = await cards.nth(i).locator('h5.title').textContent();`
4. **Conditional Action:** When `title` matches the desired target string, perform actions strictly within that parent card container:
   `await cards.nth(i).locator('button:has-text("Add to Cart")').click();`
5. **Early Exit (`break`):** Execute `break` to exit the loop immediately after finding and clicking the target card.

---

## Key Characteristics

- **Scoped Searching:** Prevents strict mode violations by restricting search scope to a single container DOM element.
- **Dynamic Catalog Support:** Works regardless of where a product sits in a list array (1st, 5th, or 50th position).
- **Loop Optimization:** Using `break` stops unnecessary DOM iterations once the target is found.

---

## Common Mistakes

- **Accidentally using `page.locator()` inside a loop instead of the chained container locator:** Writing `page.locator('button')` inside `for` loops. This searches the entire page globally rather than scoping inside `cards.nth(i)`.
- **Forgetting `break` after finding target elements:** Allowing loops to continue iterating over remaining catalog items after the target item has already been added to the cart.

---

## Canonical Code Example

```javascript
// Dynamic product search and scoped button click via locator chaining
const { test, expect } = require('@playwright/test');

test('dynamically locate product card and click add to cart', async ({ page }) => {
  await page.goto('https://example.com/shop');

  const targetProductName = 'ZARA COAT 3';
  const products = page.locator('.card-body');

  // Wait for product cards to load in DOM
  await products.first().waitFor();
  const count = await products.count();

  // Iterate over product card containers
  for (let i = 0; i < count; ++i) {
    // CHAINING: Scope search strictly inside current card container (products.nth(i))
    const currentCard = products.nth(i);
    const cardTitle = await currentCard.locator('b').textContent();

    if (cardTitle.trim() === targetProductName) {
      // CHAINING: Click 'Add to Cart' button INSIDE this specific matching card!
      await currentCard.locator('button:has-text("Add to Cart")').click();
      console.log(`Successfully added "${targetProductName}" to cart.`);
      break; // Exit loop immediately
    }
  }
});
```

---

## Key Takeaways

- Locator chaining (`parent.locator(child)`) restricts search scope to a single DOM container.
- Essential for operating on dynamic lists, tables, and card grids.
- Use `break` to terminate iteration loops once target actions complete.

---

## Related

- [[pw-multiple-element-locators]] — Indexing multi-element selectors
- [[pw-locators-css-selectors]] — Creating base selectors
- [[MOC - Playwright Locators and Actions]]
