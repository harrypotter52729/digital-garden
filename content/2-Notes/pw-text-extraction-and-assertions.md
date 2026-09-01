---
id: 202608152010
title: Text Extraction with textContent() and toContainText Assertions
aliases:
  - textContent
  - toContainText
  - text extraction
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Text Extraction with textContent() and toContainText Assertions

---

> **TL;DR:** `textContent()` extracts raw text from a DOM element like copying a phrase to your clipboard, while `toContainText()` acts as an automated proofreader that verifies whether a specific substring exists inside that element.

---

## Why This Exists

Validating UI output requires extracting text rendered on web pages (e.g., error banners, order confirmation IDs, user greeting cards). Playwright provides `locator.textContent()` to extract text strings asynchronously, and web-first assertions (`toHaveText()`, `toContainText()`) to assert expected text values with built-in auto-retry capabilities.

---

## Mental Model

Imagine reading a physical sign outside a restaurant.
- **`textContent()`:** Reading the entire text on the sign aloud ("Welcome to Joe's Diner, Open 24/7") and writing it down on a notepad (storing it in a JavaScript string variable).
- **`toContainText('Joe\'s Diner')`:** Standing in front of the sign and waiting until the neon lights flip on to confirm the sign contains the specific words "Joe's Diner".

---

## How It Works

1. **`locator.textContent()`:**
   - Retrieves the full text content of the target DOM element (including hidden child text nodes).
   - Returns a `Promise<string | null>`.
   - Automatically waits for the locator element to attach to the DOM before reading text.
2. **`await expect(locator).toContainText(expectedSubstring)`:**
   - Web-first assertion that repeatedly inspects the element's text over the assertion timeout window.
   - Passes if `expectedSubstring` is found anywhere inside the element's rendered text (case-sensitive or substring match).

---

## Key Characteristics

- **Auto-Wait Enabled:** `textContent()` automatically waits for target element attachment before extracting string values.
- **Sub-String Matching:** `toContainText()` verifies partial text matches, making tests resilient against minor punctuation changes.
- **Full Text Verification:** `toHaveText()` requires an exact string match across the entire element text.

---

## Common Mistakes

- **Forgetting `await` on `textContent()`:** Calling `const text = page.locator('.msg').textContent()` without `await` stores a pending `Promise` object rather than the string content.
- **Using `textContent()` inside `expect()` instead of Web-First assertions:** Writing `expect(await locator.textContent()).toContain('Error')` instead of `await expect(locator).toContainText('Error')`. The former evaluates once synchronously without auto-retrying, causing race condition failures!

---

## Code Comparison Matrix

### Incorrect (Manual Extraction — Flaky without Auto-Retry)

```javascript
// FLAKY: Evaluates text only ONCE at microsecond zero. Fails if text loads dynamically!
const errorText = await page.locator('.alert-danger').textContent();
expect(errorText).toContain('Invalid credentials');
```

### Correct (Web-First Assertion — Auto-Retrying & Reliable)

```javascript
// RELIABLE: Auto-retries over 5 seconds until text renders in the DOM
const errorBanner = page.locator('.alert-danger');
await expect(errorBanner).toContainText('Invalid credentials');
```

---

## Canonical Code Example

```javascript
// Extracting text and asserting dynamic messaging in Playwright
const { test, expect } = require('@playwright/test');

test('extract text and verify login error message', async ({ page }) => {
  await page.goto('https://example.com/login');

  // Submit invalid credentials
  await page.fill('#username', 'wrong_user');
  await page.fill('#password', 'wrong_pass');
  await page.click('#submit-btn');

  const errorBanner = page.locator('.error-message');

  // 1. Web-First Substring Assertion (Recommended)
  await expect(errorBanner).toContainText('Incorrect username');

  // 2. Web-First Exact Match Assertion
  await expect(errorBanner).toHaveText('Incorrect username or password.');

  // 3. Extracting raw text string for custom logging or regex parsing
  const rawMsg = await errorBanner.textContent();
  console.log(`Captured UI Banner Text: "${rawMsg.trim()}"`);
});
```

---

## Key Takeaways

- Use `locator.textContent()` to retrieve raw string values from DOM elements.
- Prefer web-first assertions (`await expect(locator).toContainText()`) over manual string assertions to benefit from automatic retries.
- `toContainText()` checks partial substring matches; `toHaveText()` checks exact full string matches.

---

## Related

- [[pw-test-assertions-expect]] — Web-first assertion engine
- [[pw-auto-waiting]] — Auto-waiting on element attachment
- [[MOC - Playwright Assertions]]
