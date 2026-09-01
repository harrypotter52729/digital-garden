---
id: 202608152027
title: Mitigating Network Latency with Keypress Delay Options
aliases:
  - keypress delay
  - pressSequentially delay
  - handling slow servers
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/execution
date_created: 2026-08-15
mastery_level: 1
---

# Mitigating Network Latency with Keypress Delay Options

---

> **TL;DR:** Adding a `{ delay: 150 }` option to `pressSequentially()` acts like typing with deliberate pauses between keystrokes—giving slow backend servers enough breathing room to process AJAX calls and render auto-suggest dropdowns without dropping characters.

---

## Why This Exists

When testing applications over slow network connections or under heavy backend server load, rapid character streaming (typing 10 characters in 2 milliseconds) can overwhelm AJAX request handlers. Server-side debouncers drop intermediate characters or miss fast input events entirely. Playwright provides the `{ delay: milliseconds }` option to introduce human-like inter-key delays.

---

## Mental Model

Imagine a receptionist taking down a phone number. If you recite all 10 digits in 1 second, the receptionist misses digits 3 through 7 and asks you to repeat. If you pause 150 milliseconds after each digit ("5... 5... 5..."), the receptionist writes down every digit accurately on the first attempt.

---

## How It Works

1. **Invocation:** Call `locator.pressSequentially(text, { delay: 150 })`.
2. **Execution Timing:**
   - Types `'i'` $\rightarrow$ Pauses 150ms $\rightarrow$ Types `'n'` $\rightarrow$ Pauses 150ms $\rightarrow$ Types `'d'`.
3. **AJAX Response Window:** The 150ms inter-key pause provides sufficient time for DOM event loops and backend debouncers (e.g., RxJS `debounceTime(100)`) to dispatch network requests and display dynamic search overlays.

---

## Key Characteristics

- **Configurable Inter-Key Latency:** Set exact millisecond pauses between individual keystrokes.
- **Prevents Flaky Network Tests:** Resolves dropped keypress events on high-latency staging environments.
- **Human Typing Emulation:** Simulates realistic human typing cadences during input testing.

---

## Common Mistakes

- **Setting excessive delays (e.g., `{ delay: 2000 }`):** Setting delays to several seconds per key slows down overall test execution unnecessarily. Keep delays between 50ms and 150ms.
- **Using `fill()` when keypress delays are needed:** `fill()` sets the entire string value at once without triggering inter-key delays or individual `keyup` events.

---

## Canonical Code Example

```javascript
// Mitigating network latency during auto-suggestive search using keypress delays
const { test, expect } = require('@playwright/test');

test('search with keypress delay for high-latency backend', async ({ page }) => {
  await page.goto('https://example.com/checkout');

  const countryInput = page.locator('input[placeholder*="Select Country"]');

  // Type 'ind' with a 150ms delay between each keystroke to accommodate server debouncing
  await countryInput.pressSequentially('ind', { delay: 150 });

  // Wait for dynamic search overlay results container
  const resultsContainer = page.locator('.ta-results');
  await resultsContainer.waitFor({ state: 'visible' });

  // Assert target option is rendered
  await expect(resultsContainer.locator('button:has-text("India")')).toBeVisible();
});
```

---

## Key Takeaways

- Use `{ delay: 150 }` in `pressSequentially()` to handle server debouncing and slow network latency.
- Keeps keystroke intervals predictable for reactive input handlers.
- Ideal for auto-suggest search boxes and debounced API endpoints.

---

## Related

- [[pw-auto-suggestive-dropdowns-character-streaming]] — Auto-suggestive dropdown handling
- [[pw-element-actions-fill-vs-type]] — `fill()` vs `pressSequentially()`
- [[MOC - Playwright Execution]]
