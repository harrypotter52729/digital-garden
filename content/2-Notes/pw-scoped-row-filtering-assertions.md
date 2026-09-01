---
id: 202608152055
title: Dynamic Scoped Element Filtering via getByRole and filter
aliases:
  - scoped row filtering
  - getByRole row filter
  - table row assertions
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Dynamic Scoped Element Filtering via getByRole and filter

---

> **TL;DR:** `page.getByRole('row').filter({ hasText: 'Mango' })` isolates the specific table row containing the string "Mango"—allowing you to scope assertions or click child cells within that row cleanly without CSS index guessing.

---

## Why This Exists

Asserting cell values in dynamic HTML tables using static index locators (e.g., `tbody tr:nth-child(3) td:nth-child(4)`) leads to flaky test failures whenever table rows re-sort. Playwright allows tests to query all rows semantically via `getByRole('row')`, filter down to the specific row matching a text predicate (`.filter({ hasText: 'Mango' })`), and scope assertions to child elements strictly inside that row.

---

## Mental Model

Imagine inspecting a stack of student test papers.
- **Index Guessing Approach:** Grabbing page #3 from the stack and reading question #2 without checking whose name is on the page. If the papers were shuffled, you grade the wrong student.
- **Scoped Row Filtering (`getByRole + filter`):** Asking the assistant: "Give me the test paper belonging to **John Doe** (`filter({ hasText: 'John Doe' })`). Once John Doe's paper is in hand, read question #2 on that paper."

---

## How It Works

1. **Target Semantic ARIA Rows:** Query all table rows using accessible roles:
   `const allRows = page.getByRole('row');`
2. **Filter Target Row by Text Predicate:**
   `const targetRow = allRows.filter({ hasText: targetSearchText });`
3. **Scoped Assertion:** Query child elements inside `targetRow` and assert value:
   `await expect(targetRow.locator('.price-cell')).toHaveText('350');`

---

## Key Characteristics

- **Resilient to Table Re-Ordering:** Works regardless of whether "Mango" is on row 1, 5, or 10.
- **Clean Scoped Searching:** Eliminates complex parent-to-child XPath expressions (`//tr[td[text()="Mango"]]/td[4]`).
- **Semantic ARIA Accessibility:** Uses standard ARIA table roles (`row`, `cell`, `table`).

---

## Common Mistakes

- **Filtering with text that matches multiple rows:** Passing a generic filter string like `.filter({ hasText: 'Fruit' })` when 10 rows contain the word "Fruit". Ensure the filter text uniquely identifies a single row.
- **Forgetting that `filter()` returns a new Locator:** Forgetting to chain locators off the returned filtered row object.

---

## Canonical Code Example

```javascript
// Scoped table row filtering and child cell assertion
const { test, expect } = require('@playwright/test');

test('locate table row by product text and assert updated price cell', async ({ page }) => {
  await page.goto('https://example.com/inventory');

  const targetFruit = 'Mango';
  const expectedPrice = '350';

  // 1. Target all semantic table rows and filter strictly to the row containing 'Mango'
  const targetRow = page.getByRole('row').filter({ hasText: targetFruit });

  // 2. Assert that target row is visible in DOM
  await expect(targetRow).toBeVisible();

  // 3. Scope assertion strictly within targetRow child cells
  const priceCell = targetRow.locator('#cell-price-val');
  await expect(priceCell).toHaveText(expectedPrice);
});
```

---

## Key Takeaways

- Use `page.getByRole('row').filter({ hasText: 'Item' })` to target dynamic table rows.
- Eliminates hardcoded `tr:nth-child()` indices and complex XPath syntax.
- Scope child cell queries and assertions strictly inside the filtered row locator.

---

## Related

- [[pw-get-by-role-accessible-locators]] — Accessible role locators
- [[pw-file-download-upload-spreadsheet-automation]] — Excel upload/download testing
- [[MOC - Playwright Assertions]]
