---
id: 202608152028
title: Capturing and Extracting Dynamic Order IDs
aliases:
  - dynamic order id
  - order id extraction
  - capturing dynamic data
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Capturing and Extracting Dynamic Order IDs

---

> **TL;DR:** Capturing a dynamic order ID is like snatching your claim ticket number at a coat check—you extract the unique string from the confirmation screen, store it in a variable, and use it later to verify your receipt in the order history ledger.

---

## Why This Exists

E-commerce checkout flows generate random, unique confirmation IDs (e.g., `| 65a83b9e120d440012f5a28f |`) upon order placement. Tests cannot hardcode static expected order IDs. Playwright scripts must extract the generated string at runtime, clean any surrounding wrapper characters using string parsing or regex, and pass it downstream to validate subsequent order history tables.

---

## Mental Model

Imagine receiving a paper ticket with decorative borders: `*** TICKET #84920 ***`.
To check if your ticket number is listed on the digital announcement board, you don't search for the asterisks `***`. You trim the decorative borders away to isolate the clean ticket ID `84920`, storing it in memory to match against the board.

---

## How It Works

1. **Order Submission:** Perform checkout and wait for the confirmation page.
2. **Text Extraction:** Extract raw confirmation text using `await locator.textContent()`.
3. **String Sanitization:**
   - Use `String.prototype.split('|')` or `String.prototype.trim()` to strip vertical bar delimiters or whitespace.
   - Example: `"| 65a83b |".split('|')[1].trim()` yields `"65a83b"`.
4. **Downstream Assertion:** Pass the sanitized `orderId` variable into order history table search functions or `expect()` assertions.

---

## Key Characteristics

- **Dynamic Data Preservation:** Enables end-to-end data validation without relying on hardcoded static test data.
- **String Parsing Cleanliness:** Strips extraneous HTML wrapper formatting (bars, brackets, spaces).
- **Cross-Page Data Transfer:** Stores the dynamic string in test scope for post-checkout verification.

---

## Common Mistakes

- **Comparing raw un-sanitized strings:** Attempting to match `"| 65a83b |"` directly against table cells that display `65a83b`. The test fails due to hidden spaces or bar characters.
- **Forgetting `await` on text extraction:** Calling `page.locator('.order-id').textContent()` without `await`, resulting in string operations executing on a pending Promise.

---

## Canonical Code Example

```javascript
// Extracting dynamic order IDs and sanitizing string formats
const { test, expect } = require('@playwright/test');

test('capture dynamic order id on confirmation page', async ({ page }) => {
  await page.goto('https://example.com/checkout');

  // Submit order...
  await page.click('#place-order-btn');

  // Locate raw confirmation label (e.g., "| 672e10ab99f43001a1234567 |")
  const orderIdLocator = page.locator('label.ng-star-inserted');
  await orderIdLocator.waitFor({ state: 'visible' });

  // 1. Extract raw text content string
  const rawText = await orderIdLocator.textContent();
  console.log(`Raw Confirmation Text: "${rawText}"`);

  // 2. Sanitize string (split by '|' and trim whitespace)
  const cleanedOrderId = rawText.split('|')[1].trim();
  console.log(`Cleaned Dynamic Order ID: "${cleanedOrderId}"`);

  // 3. Assert sanitized ID length/format
  expect(cleanedOrderId.length).toBeGreaterThan(10);
});
```

---

## Key Takeaways

- Extract dynamic runtime strings using `await locator.textContent()`.
- Sanitize strings using `.split()` and `.trim()` to remove wrapper formatting.
- Store cleaned values in variables for cross-page downstream assertion.

---

## Related

- [[pw-html-table-traversal-scoping]] — Finding extracted order IDs in tables
- [[pw-e2e-ecommerce-test-strategy]] — End-to-End E-Commerce workflow
- [[MOC - Playwright Assertions]]
