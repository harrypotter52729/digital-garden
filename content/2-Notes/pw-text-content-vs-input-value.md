---
id: 202608152019
title: Text Extraction vs Input Value: textContent() vs inputValue()
aliases:
  - textContent vs inputValue
  - inputValue method
  - reading input text
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Text Extraction vs Input Value: textContent() vs inputValue()

---

> **TL;DR:** Use `textContent()` to read inner text rendered between HTML tags (like `<div>Hello</div>`), but use `inputValue()` to read dynamic text typed inside form input fields (`<input value="user_typed_this">`).

---

## Why This Exists

Developers often encounter blank string returns when trying to read user-entered text from form `<input>` or `<textarea>` elements using `locator.textContent()`. HTML form elements store user input inside their dynamic `value` attribute, rather than as inner HTML text nodes. Playwright provides `inputValue()` specifically to retrieve form field values.

---

## Mental Model

Imagine a picture frame vs a dry-erase whiteboard.
- **`textContent()` (Picture Frame):** Reads the poster printed *inside* the glass frame (`<span>`, `<div>`, `<p>`). If you try to write on the glass with a marker, `textContent()` ignores the marker writing and reads only the static poster underneath.
- **`inputValue()` (Dry-Erase Board):** Reads the marker text written on the surface of an `<input>` box by a user or script at runtime.

---

## How It Works

### `locator.textContent()`
- Reads inner DOM text node children of elements like `<div>`, `<span>`, `<h1>`, `<button>`.
- Returns text attached to the initial DOM structure.
- Returns empty string `""` when called on input elements (`<input type="text">`) because inputs do not contain inner text nodes.

### `locator.inputValue()`
- Reads the current live `value` attribute of form input elements (`<input>`, `<textarea>`, `<select>`).
- Captures text typed dynamically by users or populated programmatically via `fill()`.

---

## Comparison Matrix

| Target Element Type | Extraction Method to Use | Example HTML | Returned Value |
| :--- | :--- | :--- | :--- |
| **`<div>`, `<span>`, `<p>`** | `locator.textContent()` | `<div>Welcome Back</div>` | `'Welcome Back'` |
| **`<input type="text">`** | `locator.inputValue()` | `<input value="admin@test.com">` | `'admin@test.com'` |
| **`<textarea>`** | `locator.inputValue()` | `<textarea>Comments...</textarea>` | `'Comments...'` |
| **`<select>`** | `locator.inputValue()` | `<select><option value="opt1">` | `'opt1'` |

---

## Common Mistakes

- **Calling `textContent()` on an `<input>` field:** Writing `const text = await page.locator('#username').textContent();`. `text` evaluates to `""` (empty string), leading to false assertion failures.
- **Confusing `inputValue()` with `fill()`:** `fill('text')` writes text into a field; `inputValue()` reads text out of a field.

---

## Canonical Code Example

```javascript
// Demonstrating the difference between textContent() and inputValue()
const { test, expect } = require('@playwright/test');

test('demonstrate textContent vs inputValue extraction', async ({ page }) => {
  await page.goto('https://example.com/form');

  // 1. Populate an input field dynamically
  const usernameInput = page.locator('input#username');
  await usernameInput.fill('john_doe');

  // INCORRECT: textContent() returns empty string "" for <input> elements!
  const incorrectText = await usernameInput.textContent();
  console.log(`textContent result: "${incorrectText}"`); // Output: ""

  // CORRECT: inputValue() reads the live value attribute!
  const correctValue = await usernameInput.inputValue();
  console.log(`inputValue result: "${correctValue}"`); // Output: "john_doe"
  expect(correctValue).toBe('john_doe');

  // 2. Reading static label text using textContent()
  const headingText = await page.locator('h1.page-title').textContent();
  console.log(`Heading text: "${headingText.trim()}"`);
});
```

---

## Key Takeaways

- `textContent()` extracts inner HTML text nodes from structural elements (`<div>`, `<span>`).
- `inputValue()` extracts dynamic user input values from form controls (`<input>`, `<textarea>`).
- Calling `textContent()` on `<input>` tags returns an empty string `""`.

---

## Related

- [[pw-text-extraction-and-assertions]] — Reading static text content
- [[pw-element-actions-fill-vs-type]] — Populating input fields
- [[MOC - Playwright Locators and Actions]]
