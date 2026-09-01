---
id: 202608152014
title: Interacting with Static select Dropdowns with selectOption()
aliases:
  - static dropdowns
  - selectOption
  - select dropdown
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Interacting with Static select Dropdowns with selectOption()

---

> **TL;DR:** `locator.selectOption()` is an automated picker for native HTML `<select>` elements—it selects options instantly by value, visible text, or index in a single clean operation.

---

## Why This Exists

Native HTML `<select>` dropdown menus contain child `<option>` elements. Clicking a `<select>` element to open the option overlay and then clicking a child `<option>` item via standard user clicks is clunky and unreliable across different operating system rendering engines. Playwright provides `locator.selectOption()` to handle static `<select>` elements directly.

---

## Mental Model

Imagine picking a item from a vending machine.
- **Manual Click Approach:** Click the vending machine glass door, wait for the door to open, reach inside, and select snack #3.
- **`selectOption()` Approach:** Press button `C3` on the external keypad. The vending machine dispenses snack #3 directly.

---

## How It Works

1. **Target Identification:** Locate the parent `<select>` element (e.g., `page.locator('select#role')`).
2. **`selectOption()` Methods:** Pass one of the following selection criteria:
   - **Value Attribute:** `selectOption('consultant')` (matches `<option value="consultant">`).
   - **Label / Visible Text:** `selectOption({ label: 'Consultant' })`.
   - **Index:** `selectOption({ index: 2 })` (0-based index selection).
3. **Event Dispatch:** Playwright updates the DOM value and dispatches `change` and `input` events automatically.

---

## Key Characteristics

- **Single Action Selection:** Does not require multi-step click-open and click-item steps.
- **Multiple Criteria Support:** Select by `value`, `label` (visible text), or `index`.
- **Multi-Select Support:** Accepts an array of values for `<select multiple>` elements (e.g., `selectOption(['val1', 'val2'])`).

---

## Common Mistakes

- **Confusing native `<select>` dropdowns with custom dynamic `<div>`/`<ul>` dropdowns:** `selectOption()` works **ONLY** on native HTML `<select>` elements. For custom React/Bootstrap select components built with `<div>` elements, use standard `click()` and locator actions!
- **Passing visible text instead of value attribute without specifying `{ label }`:** If `<option value="consult_val">Consultant</option>`, calling `selectOption('Consultant')` fails because `'Consultant'` is the visible label, not the `value` attribute! Use `selectOption({ label: 'Consultant' })` instead.

---

## Canonical Code Example

```javascript
// Interacting with native static HTML <select> dropdowns in Playwright
const { test, expect } = require('@playwright/test');

test('select options from static dropdown', async ({ page }) => {
  await page.goto('https://example.com/register');

  const roleDropdown = page.locator('select#user-role');

  // 1. Select by option 'value' attribute (<option value="consultant">)
  await roleDropdown.selectOption('consultant');

  // 2. Select by visible label text (<option value="...">Consultant</option>)
  await roleDropdown.selectOption({ label: 'Consultant' });

  // 3. Select by zero-based option index (2nd item in list)
  await roleDropdown.selectOption({ index: 1 });

  // 4. Verify selected value using expect assertion
  await expect(roleDropdown).toHaveValue('consultant');
});
```

---

## Key Takeaways

- Use `locator.selectOption()` exclusively for native HTML `<select>` tags.
- Select options by `value` string, `{ label: 'Text' }`, or `{ index: n }`.
- Automatically dispatches `change` and `input` events to update framework state.

---

## Related

- [[pw-locators-css-selectors]] — Locating select elements
- [[pw-radio-buttons-web-popups-inspector]] — Handling radio buttons and popups
- [[MOC - Playwright Locators and Actions]]
