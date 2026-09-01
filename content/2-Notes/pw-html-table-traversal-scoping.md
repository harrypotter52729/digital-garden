---
id: 202608152029
title: Dynamic HTML Table Traversal and Row Action Scoping
aliases:
  - table traversal
  - html table automation
  - row action scoping
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Dynamic HTML Table Traversal and Row Action Scoping

---

> **TL;DR:** Table traversal iterates through `<tr>` rows, locates the cell matching your unique target order ID, and scopes your "View Details" button click strictly within that specific matching row.

---

## Why This Exists

Order history pages display data inside HTML `<table>` structures. Since order rows rearrange dynamically, hardcoding a row index (e.g., `tbody tr:nth-child(1)`) fails if a new order moves to row 3 or 5. Playwright scripts must dynamically scan table rows, locate the matching order ID cell, and scope action button clicks inside that exact row.

---

## Mental Model

Imagine searching for your name on a physical sign-up list at a clinic.
- **Hardcoded Index (Row 1):** Tapping the "View File" button on line #1 without reading the name. If someone else signed up before you, you open a stranger's file.
- **Dynamic Table Traversal:** Scanning down the "Name" column line by line. When your eyes spot your name on line #4, your finger slides across line #4 to tap the "View File" button on that exact line.

---

## How It Works

1. **Table Load Wait:** Wait for table rows to render (`await page.locator('tbody tr').first().waitFor()`).
2. **Row Collection & Iteration:** Collect all rows (`const rows = page.locator('tbody tr')`) and loop over `rows.count()`.
3. **Target Cell Check:** Extract the order ID cell text from the current row:
   `const rowOrderId = await rows.nth(i).locator('th, td').first().textContent();`
4. **Matching & Scoped Action:** If `rowOrderId` matches target `orderId`:
   - Scope the button click inside the current row:  
     `await rows.nth(i).locator('button:has-text("View")').click();`
   - Execute `break` to exit the loop.

---

## Key Characteristics

- **Dynamic Row Targeting:** Locates target data regardless of row sorting order.
- **Scoped Action Execution:** Prevents clicking the wrong row's action button.
- **Table Synchronization:** Requires pre-waiting on `tbody tr` rendering before evaluating row counts.

---

## Common Mistakes

- **Forgetting to wait for table rendering:** Calling `rows.count()` before table API data populates, receiving `0` rows.
- **Clicking global button selectors:** Calling `page.click('button:has-text("View")')` instead of scoping inside `rows.nth(i)`. Global clicks target whichever button appears first on the page.

---

## Canonical Code Example

```javascript
// Dynamic HTML table traversal and scoped row button click
const { test, expect } = require('@playwright/test');

test('search order history table and click view button for matching order', async ({ page }) => {
  await page.goto('https://example.com/orders');

  const targetOrderId = '672e10ab99f43001a1234567';
  const tableRows = page.locator('tbody tr');

  // 1. Wait for table rows to render in DOM
  await tableRows.first().waitFor({ state: 'visible' });
  const rowCount = await tableRows.count();

  // 2. Iterate table rows dynamically
  for (let i = 0; i < rowCount; ++i) {
    const currentRow = tableRows.nth(i);
    const rowOrderId = await currentRow.locator('th').textContent(); // Order ID cell

    // 3. Match target ID (using includes or equality)
    if (targetOrderId.includes(rowOrderId.trim())) {
      // 4. Scope action button click strictly inside this matching row!
      await currentRow.locator('button:has-text("View")').click();
      console.log(`Clicked View button for Order ID: ${rowOrderId.trim()}`);
      break; // Exit loop
    }
  }

  // Verify navigation to order details view
  await expect(page.locator('.order-summary-title')).toBeVisible();
});
```

---

## Key Takeaways

- Dynamically scan `tbody tr` rows to find target order IDs without relying on hardcoded indices.
- Scope action clicks strictly within `rows.nth(i)` to hit the correct row's buttons.
- Always wait for table rows to render before inspecting row counts.

---

## Related

- [[pw-order-id-extraction]] — Extracting order IDs
- [[pw-dynamic-element-iteration-chaining]] — Scoped locator chaining
- [[MOC - Playwright Locators and Actions]]
