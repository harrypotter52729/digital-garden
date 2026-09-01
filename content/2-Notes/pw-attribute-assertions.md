---
id: 202608152017
title: Attribute Verification Assertions with toHaveAttribute
aliases:
  - toHaveAttribute
  - attribute verification
  - checking dom attributes
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Attribute Verification Assertions with toHaveAttribute

---

> **TL;DR:** `await expect(locator).toHaveAttribute()` is an automated inspector that verifies whether a DOM element possesses a specific HTML attribute and matching value—allowing you to test blinking links, active tab classes, disabled states, or target URLs.

---

## Why This Exists

Visual CSS styles (like blinking text effects, active navigation tab highlights, or disabled button appearances) are often implemented by adding dynamic CSS classes or custom attributes to DOM elements. Playwright provides `toHaveAttribute()` to assert the presence and exact value of DOM attributes without relying on fragile visual pixel checks.

---

## Mental Model

Imagine inspecting an employee's security ID badge.
- **Visual Inspection:** Looking at whether their shirt looks green or blue (fragile, dependent on lighting).
- **Attribute Verification (`toHaveAttribute('data-clearance', 'level-3')`):** Flipping the security badge over and checking the exact printed text field (`data-clearance="level-3"`). Reliable and deterministic.

---

## How It Works

1. **Target Identification:** Select the target DOM element locator (`const link = page.locator('a.blinking-link')`).
2. **Assertion Call:** `await expect(locator).toHaveAttribute(attributeName, expectedValue)`:
   - `attributeName`: String key of the attribute (e.g., `'class'`, `'href'`, `'disabled'`, `'data-testid'`).
   - `expectedValue`: Expected string value or RegExp pattern.
3. **Auto-Retrying Execution:** Retries over the assertion timeout window until the element attribute matches.

---

## Key Characteristics

- **RegExp Matching:** Accepts Regular Expression patterns (e.g., `toHaveAttribute('class', /blinking-text/)`) for partial class list verification.
- **Auto-Retrying:** Web-first assertion that waits for dynamic JavaScript state updates.
- **Multi-Attribute Verification:** Verify link destinations (`href`), input requirements (`required`), or component states (`aria-expanded`).

---

## Common Mistakes

- **Exact string matching failure when an element has multiple CSS classes:** Calling `toHaveAttribute('class', 'blinking-text')` when the element actually has `class="nav-item blinking-text active"`. Use RegExp `/blinking-text/` or `toHaveClass(/blinking-text/)` for class list membership testing!
- **Forgetting `await` on `toHaveAttribute()`:** Omitting `await` causes the assertion to return a pending Promise, bypassing verification.

---

## Canonical Code Example

```javascript
// Verifying DOM element attributes in Playwright
const { test, expect } = require('@playwright/test');

test('verify element attributes and dynamic class states', async ({ page }) => {
  await page.goto('https://example.com/demo');

  const blinkingLink = page.locator('a#blinking-doc-link');
  const submitButton  = page.locator('button#submit-btn');

  // 1. Verify link target destination (href attribute)
  await expect(blinkingLink).toHaveAttribute('href', 'https://example.com/docs');

  // 2. Verify class attribute using RegExp pattern for partial match
  await expect(blinkingLink).toHaveAttribute('class', /blinking-text/);

  // 3. Dedicated toHaveClass shortcut helper
  await expect(blinkingLink).toHaveClass(/blinking-text/);

  // 4. Verify custom data attributes or disabled states
  await expect(submitButton).toHaveAttribute('disabled', '');
});
```

---

## Key Takeaways

- Use `await expect(locator).toHaveAttribute('attr', 'value')` to verify HTML attributes.
- Pass RegExp patterns (`/value/`) to test partial string matches across multi-class attributes.
- Use the dedicated `toHaveClass()` shortcut for class membership checks.

---

## Related

- [[pw-test-assertions-expect]] — Web-first assertion engine
- [[pw-checkbox-radio-assertions]] — Checked state assertions
- [[MOC - Playwright Assertions]]
