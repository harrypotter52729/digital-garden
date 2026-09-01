---
id: 202608152050
title: Page and Element Screenshot Capture
aliases:
  - page.screenshot
  - locator.screenshot
  - screenshot capture
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/debugging
date_created: 2026-08-15
mastery_level: 1
---

# Page and Element Screenshot Capture

---

> **TL;DR:** `page.screenshot({ path: 'full.png' })` captures full-page visual evidence, while `locator.screenshot({ path: 'element.png' })` crops visual screenshots strictly to the bounding box of a specific element.

---

## Why This Exists

When debugging test failures or documenting visual UI states for compliance audit logs, console text outputs are insufficient. Playwright provides native **Screenshot Capture** methods at both the full page level (`page.screenshot()`) and individual component element level (`locator.screenshot()`).

---

## Mental Model

Imagine taking photos at a crime scene.
- **Full Page Screenshot (`page.screenshot()`):** Taking a wide-angle panoramic photo of the entire room (`path: 'room.png'`).
- **Element-Level Screenshot (`locator.screenshot()`):** Using a macro zoom lens to crop a close-up photo strictly of the door lock mechanism (`locator('#lock').screenshot()`).

---

## How It Works

### Full Page Screenshot
- `await page.screenshot({ path: 'screenshots/page.png', fullPage: true })`
- **`fullPage: true`:** Scrolls and stitches together long scrollable web pages into a single full-length image file.

### Element-Level Screenshot
- `await page.locator('#target-card').screenshot({ path: 'screenshots/card.png' })`
- Automatically crops PNG image boundaries strictly to the target DOM element's visible bounding box on screen.

---

## Key Characteristics

- **Automatic Format Detection:** Saves PNG or JPEG image files based on path extension (`.png`, `.jpg`).
- **Full-Page Stitching:** Captures complete scrollable page content beyond current viewport bounds when `fullPage: true` is set.
- **Component Cropping:** Isolates individual widgets, charts, or error banners for target verification.

---

## Common Mistakes

- **Forgetting `await` on screenshot methods:** Writing `page.screenshot({ path: 'out.png' })` without `await`. Execution finishes before the file writes to disk.
- **Saving screenshots to non-existent directory paths:** Providing `path: 'nested/dir/out.png'` when `nested/dir` folder has not been created. (Create target directory first or save directly to root/artifacts).

---

## Canonical Code Example

```javascript
// Demonstrating full-page and element-level screenshot capture in Playwright
const { test, expect } = require('@playwright/test');

test('capture full page and partial element screenshots', async ({ page }) => {
  await page.goto('https://example.com/practice');

  // 1. Capture full-page scrollable screenshot
  await page.screenshot({
    path: 'screenshots/full_page_proof.png',
    fullPage: true,
  });

  // 2. Locate specific target element (e.g., dynamic text box)
  const textBoxLocator = page.locator('#displayed-text');
  await expect(textBoxLocator).toBeVisible();

  // 3. Capture partial element-level screenshot (Cropped strictly to textBox bounds)
  await textBoxLocator.screenshot({
    path: 'screenshots/partial_textbox_element.png',
  });
});
```

---

## Key Takeaways

- Use `page.screenshot({ fullPage: true })` for complete scrollable page visual capture.
- Use `locator.screenshot()` to crop screenshots to a single component or widget.
- Always prefix screenshot calls with `await`.

---

## Related

- [[pw-visual-regression-testing}] — Visual regression testing
- [[pw-html-reporting-artifacts]] — Attaching screenshots to reports
- [[MOC - Playwright Execution]]
