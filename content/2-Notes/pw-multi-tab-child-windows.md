---
id: 202608152018
title: Multi-Tab and Child Window Handling with waitForEvent('page')
aliases:
  - child windows
  - multi-tab testing
  - waitForEvent page
  - promise.all windows
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/architecture
date_created: 2026-08-15
mastery_level: 1
---

# Multi-Tab and Child Window Handling with waitForEvent('page')

---

> **TL;DR:** `Promise.all([ context.waitForEvent('page'), locator.click() ])` is like holding open a net before throwing a ball—it sets up an event listener to catch the newly opening browser tab at the exact moment the trigger link is clicked, eliminating window race conditions.

---

## Why This Exists

When users click links with `target="_blank"`, browsers open new tabs or pop-up windows. In traditional automation, switching window handles required polling active handles or explicit delay sleeps. Playwright provides native event listening (`context.waitForEvent('page')`) to capture newly spawned `Page` objects asynchronously and reliably.

---

## Mental Model

Imagine standing at a train station.
- **Polling Handle Approach (Legacy):** Click a button to dispatch a new train, wait 3 seconds, and look up at the departures board to search for new train numbers.
- **Event Promise Approach (`Promise.all`):** Post a security guard at the gate (`waitForEvent('page')`) *before* pushing the green button (`click()`). The moment the gate opens and the new train rolls out, the guard catches the train handle (`newPage`) and hands it to you immediately.

---

## How It Works

1. **Event Promise Registration:** Call `context.waitForEvent('page')` to create a Promise that resolves when a new tab opens inside the `BrowserContext`.
2. **Synchronized Trigger Execution:** Wrap both the event listener registration and the click action inside `Promise.all([ ... ])`:
   ```javascript
   const [newPage] = await Promise.all([
     context.waitForEvent('page'),
     locator.click(),
   ]);
   ```
3. **Independent Page Operations:** The resulting `newPage` variable represents an independent Playwright `Page` object. Perform actions on `newPage` for the child tab and on `page` for the parent tab.

---

## Key Characteristics

- **Race Condition Prevention:** Registering the listener prior to or concurrently with the click action ensures no popups are missed.
- **Independent Page Isolation:** Parent (`page`) and child (`newPage`) operate as distinct, isolated tab instances within the same `BrowserContext`.
- **Cross-Tab Data Exchange:** Extract data (e.g., promo codes or tokens) from `newPage` and type them directly into `page`.

---

## Common Mistakes

- **Awaiting `click()` BEFORE registering `waitForEvent('page')`:** Writing `await locator.click(); const newPage = await context.waitForEvent('page');`. If the child tab opens fast, the event fires *before* the listener attaches, causing the test to hang indefinitely!
- **Attempting child tab actions on the parent `page` object:** Calling `page.locator(...)` expecting it to target elements inside `newPage`.

---

## Canonical Code Example

```javascript
// Handling child windows and tab switching in Playwright
const { test, expect } = require('@playwright/test');

test('extract information from child tab and enter into parent page', async ({ page, context }) => {
  await page.goto('https://example.com/login');

  // 1. Setup Promise.all to catch newly spawned page event concurrently with click
  const [newPage] = await Promise.all([
    context.waitForEvent('page'), // Event listener for new tab creation
    page.locator('a.blinking-link').click(), // Action that opens target="_blank" child tab
  ]);

  // 2. Wait for child tab DOM to load and extract text
  await newPage.waitForLoadState();
  const infoText = await newPage.locator('.promo-code').textContent();
  console.log(`Extracted from child window: ${infoText}`);

  // 3. Switch back to parent page and enter extracted data
  await page.locator('#promo-input').fill(infoText.trim());

  // Verify parent page state
  await expect(page.locator('#promo-input')).toHaveValue(infoText.trim());
});
```

---

## Key Takeaways

- Use `Promise.all([ context.waitForEvent('page'), locator.click() ])` to capture child tabs safely.
- Always attach the event listener concurrently with the click action to prevent race conditions.
- Parent and child pages are independent `Page` objects (`page` vs `newPage`).

---

## Related

- [[pw-browser-context-page-hierarchy]] — Understanding Context and Page objects
- [[pw-async-await-execution]] — Promise execution order
- [[MOC - Playwright Execution]]
