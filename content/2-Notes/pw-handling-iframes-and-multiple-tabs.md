---
id: 202606131228
title: Handling iFrames & Multiple Tabs
aliases:
  - playwright-iframes
  - playwright-multiple-pages
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/locating-interacting
date_created: 2026-06-13
mastery_level: 1
---

# Handling iFrames & Multiple Tabs

> **TL;DR:** Interaction in Playwright is scoped to a single document page. When interacting with nested documents (**iFrames**) or elements that spawn new browser windows (**Multiple Tabs**), you must explicitly tell Playwright to switch its focus to the new document context using `frameLocator` or context event listeners.

## The Dual Monitor Analogy

Imagine you are working at a desk with a dual-monitor setup:

```
    [ Monitor 1: Main Page ]                [ Monitor 2: New Tab ]
  +--------------------------+            +--------------------------+
  | Main Website             |            | External Page            |
  |                          |            |                          |
  |  +--------------------+  |            |                          |
  |  | iFrame (Embedded)  |  |            |                          |
  |  |                    |  |            |                          |
  |  +--------------------+  |            |                          |
  +--------------------------+            +--------------------------+
```

1. **Main Page:** Your primary window. You can interact with it directly.
2. **iFrames (Picture-in-Picture):** An embedded webpage *inside* your main page. To click inside it, you cannot just click the coordinates; you have to turn your eyes to it and tell your hands to operate exclusively inside that sub-frame (`page.frameLocator()`).
3. **Multiple Tabs (Second Monitor):** Clicking a link might open a new browser tab. To work on it, you must tell your hands to leave Monitor 1 entirely and move your mouse/keyboard focus to Monitor 2 (`context.waitForEvent('page')`).

---

## 1. Handling iFrames

An `<iframe>` element embeds another HTML document into the current page. Playwright scopes iframe traversal through `frameLocator(selector)`. Once focused inside a frame locator, all subsequent query locators run *inside* that iframe's DOM.

* **Key Rule:** You do not "switch into" and "switch out of" frames manually like in older tools (e.g., Selenium). Frame locators are part of the locator chain itself, meaning Playwright automatically handles entry and exit operations.

---

## 2. Handling Multiple Tabs & Popups

New tabs or windows are spawned at the `BrowserContext` level. Since tabs share cookies and local storage (see [[pw-browser-context-and-page-model]]), they live in the same context, but are represented by different `Page` objects.

To capture a page opened by a click:
1. Listen for the `'page'` event on the browser context.
2. Trigger the action that opens the page.
3. Await the page promise to resolve to get the new `Page` reference.

---

## Canonical Code Example

This code demonstrates interacting with an embedded iFrame (such as a secure payment box) and handling an external link that launches in a new tab:

```javascript
// @ts-check
const { test, expect } = require("@playwright/test");

test("Handling embedded frames and multi-tab workflows", async ({ page, context }) => {
  await page.goto("/iframe-and-tabs-demo");

  // --- 1. Working with iFrames ---

  // Locate the iframe using a unique CSS selector
  const signupFrame = page.frameLocator("#signup-iframe");

  // All interactions inside the frame use the frame object directly
  // Playwright handles checking actionability inside the iframe transparently
  await signupFrame.getByLabel("Email Address").fill("subscriber@test.com");
  await signupFrame.getByRole("button", { name: "Subscribe" }).click();

  // Assertions can also verify values inside the frame
  await expect(signupFrame.locator("#success-message")).toHaveText("Thanks for subscribing!");

  // --- 2. Working with Multiple Tabs ---

  // We set up a listener for the page event *before* clicking the button.
  // This prevents race conditions where the tab opens before the test listener is active.
  const pagePromise = context.waitForEvent("page");

  // This button has target="_blank", opening a new tab
  await page.getByRole("link", { name: "Open Terms in New Tab" }).click();

  // Resolve the promise to get access to the new Page object
  const newTabPage = await pagePromise;

  // Wait for the new tab to complete loading
  await newTabPage.waitForLoadState();

  // Execute assertions and actions on the new page independently
  await expect(newTabPage).toHaveTitle("Terms of Service");
  await expect(newTabPage.getByRole("heading", { name: "License Rules" })).toBeVisible();

  // We can still interact with the primary page
  await page.getByRole("checkbox", { name: "I accept the terms" }).check();

  // Clean up: close the sub-tab to free memory
  await newTabPage.close();
});
```

---

## Related
* [[pw-browser-context-and-page-model]] - The architectural model explaining why pages share cache while maintaining separation.
* [[pw-built-in-locators-vs-css-xpath]] - Selecting user-facing elements within nested page trees.
