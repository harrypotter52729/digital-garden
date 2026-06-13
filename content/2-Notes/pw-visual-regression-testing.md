---
id: 202606131233
title: Visual Regression Testing
aliases:
  - playwright-visual-testing
  - snapshot-comparison
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/assertions
date_created: 2026-06-13
mastery_level: 1
---

# Visual Regression Testing

> **TL;DR:** Visual Regression Testing verifies that the look of your user interface does not change unexpectedly. Playwright does this by comparing a live screenshot of a page or element against a stored **baseline screenshot** (also called a "golden image") and flagging any pixel deviations.

## The Spot the Difference Analogy

Imagine playing a children's game of **Spot the Difference**:

```
[ Baseline Image ]               [ Live Test Image ]
   (Golden Image)                   (Actual Run)
  +--------------+                +--------------+
  |  [ Logo ]    |                |  [ Logo ]    |
  |  Home  Shop  |     ===>       |  Home  *Shop*|  <-- CSS shift changes padding!
  |  [ Banner ]  |                |  [ Banner ]  |
  +--------------+                +--------------+
                                         |
                                         v
                                  [ Diff Overlay ]
                                  (Highlights the 
                                   mismatch pixels 
                                   in bright magenta)
```

1. **The Golden Image:** You take a picture of your web app when it is working perfectly and check it into your repository.
2. **The Test run:** Playwright runs your test, takes a new screenshot, and overlays the two images.
3. **The Mismatch Report:** If a pixel color changes or an element shifts by 2 pixels, Playwright highlights the differences in **bright magenta** and fails the test.

---

## Key Configuration & Customization Options

Font rendering, browser engine binaries, and sub-pixel antialiasing can cause minor variations across environments (e.g., running locally on Windows vs running on a Linux CI server). Playwright provides configuration thresholds to accommodate this:

- **`maxDiffPixels`**: The maximum number of pixels that can differ between the actual image and the baseline before marking the test as failed.
- **`threshold`**: Controls the color difference sensitivity (from `0` to `1`). A threshold of `0.2` allows very minor color changes, while `0` requires an exact match.
- **`mask`**: An array of locators pointing to elements (like date pickers, ads, or dynamic user text) that should be greyed-out (masked) during the screenshot so they are ignored by the comparison engine.

---

## Managing Baselines

When you run visual tests for the first time, they will fail because no baseline image exists. You must generate them:
- **Command:** `npx playwright test --update-snapshots`
- This saves the golden screenshots to a directory matching your test file name (e.g., `tests/home-page.spec.js-snapshots/`).

---

## Canonical Code Example

This code illustrates capturing a visual snapshot of a specific card container while masking dynamic content:

```javascript
// @ts-check
const { test, expect } = require("@playwright/test");

test("Dashboard card visual regression validation", async ({ page }) => {
  await page.goto("/dashboard");

  const revenueCard = page.locator("#revenue-widget");
  const chartsWrapper = page.locator(".charts-container");

  // 1. Ensure the UI is fully loaded and stable first
  // Taking a screenshot before animations complete causes visual failures!
  await expect(revenueCard).toBeVisible();
  
  // 2. Element Snapshot: Visual test on a specific card locator only
  // This reduces flakiness compared to testing the whole page
  await expect(revenueCard).toHaveScreenshot("revenue-widget-baseline.png");

  // 3. Full Page Snapshot with customizations
  // Masking dynamic elements (like a changing charts container) prevents failures 
  // when the underlying chart data changes.
  await expect(page).toHaveScreenshot("dashboard-fullpage-baseline.png", {
    // Hide the charts container by rendering a gray box over it during comparison
    mask: [chartsWrapper],
    
    // Pixel tolerance settings
    maxDiffPixels: 100, // Allow up to 100 mismatched pixels
    threshold: 0.2,     // Visual sensitivity (default is 0.2)
    
    // Auto-scroll the page and stitch images together for long pages
    fullPage: true,
  });
});
```

---

## Related
* [[pw-web-first-assertions]] - Waiting for dynamic elements to finish rendering before taking screenshots.
* [[pw-playwright-config-file]] - Defining default snapshot paths and configuring visual thresholds globally.
