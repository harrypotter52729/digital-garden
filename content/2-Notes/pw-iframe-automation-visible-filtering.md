---
id: 202608152039
title: iFrame Automation and :visible Selector Filtering
aliases:
  - frameLocator
  - iframe testing
  - visible selector
  - hidden element filtering
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# iFrame Automation and :visible Selector Filtering

---

> **TL;DR:** `page.frameLocator('#iframe-id')` switches execution scope inside an embedded HTML `<iframe>`, while the `:visible` pseudo-class (`locator('button:visible')`) filters out hidden DOM elements to avoid strict mode violations.

---

## Why This Exists

HTML `<iframe>` elements embed external HTML documents inside a parent page. Direct `page.locator()` queries fail to target elements inside iframes because parent page DOM contexts cannot access embedded iframe DOM trees. Playwright provides `page.frameLocator()` to enter iframe scopes seamlessly, along with `:visible` selector filtering to bypass hidden duplicate elements.

---

## Mental Model

Imagine looking through a glass window into an adjacent room.
- **Parent Page Scope (`page.locator()`):** Standing in the main hallway trying to touch a desk inside the adjacent room. Your hand hits the glass window (`<iframe>`).
- **`frameLocator()` Scope:** Opening the door to step inside the adjacent room (`page.frameLocator('#room-frame')`). Now your hand touches the desk directly.

---

## How It Works

### iFrame Scoping with `frameLocator()`
1. **Scope Definition:** `const frame = page.frameLocator('#courses-iframe');`
2. **Scoped Execution:** Query elements inside the frame scope using `frame.locator()`:
   `await frame.locator('a:has-text("All Access")').click();`

### Hidden Element Filtering with `:visible`
- When multiple elements match a CSS selector but only one is visible on screen, append `:visible`:
  `const visibleBtn = page.locator('.btn-primary:visible');`
- Restricts Playwright's target match strictly to the element currently rendered on screen.

---

## Key Characteristics

- **Seamless iFrame Access:** Operates inside nested iframes without requiring legacy `switchTo().frame()` calls.
- **Strict Mode Violation Resolver:** Using `:visible` resolves multi-match errors when hidden duplicate elements exist in the DOM.
- **Nested Frame Support:** Chain frame locators for deeply nested frames (`page.frameLocator('#outer').frameLocator('#inner')`).

---

## Common Mistakes

- **Calling `page.locator()` on iframe child elements directly:** Attempting `page.locator('.iframe-child-btn').click()`. Throws element not found error because target lives inside a child iframe.
- **Storing stale iframe objects:** Expecting iframe locators to stay active after page navigation. Re-query `frameLocator()` when navigating.

---

## Canonical Code Example

```javascript
// Automating elements inside embedded iframes and filtering visible elements
const { test, expect } = require('@playwright/test');

test('interact with elements inside iframe using frameLocator and :visible', async ({ page }) => {
  await page.goto('https://example.com/practice');

  // 1. Target embedded iframe via ID selector
  const iframeScope = page.frameLocator('#courses-iframe');

  // 2. Click button inside iframe using :visible pseudo-class to ignore hidden duplicates
  const visibleLink = iframeScope.locator('a:has-text("All Access"):visible');
  await visibleLink.click();

  // 3. Extract text content from element inside iframe
  const textContent = await iframeScope.locator('.subscriber-count').textContent();
  console.log(`Subscriber Count inside iFrame: ${textContent.trim()}`);

  // Split and sanitize string (e.g., "13,522 Happy Subscribers" -> "13,522")
  const countNumber = textContent.split(' ')[0].trim();
  expect(countNumber).toBeTruthy();
});
```

---

## Key Takeaways

- Access embedded iframe DOM elements using `page.frameLocator('#iframe-id')`.
- Use `:visible` (e.g., `.btn:visible`) to filter out hidden duplicate elements and satisfy strict mode checks.
- Extract data inside iframes directly using `frameScope.locator().textContent()`.

---

## Related

- [[pw-javascript-dialog-and-hover-handling]] — JavaScript dialogs and hover actions
- [[pw-multi-tab-child-windows]] — Multi-tab child window handling
- [[MOC - Playwright Locators and Actions]]
