---
id: 202607282010
title: BasePage Abstraction Pattern in Playwright
aliases:
  - BasePage pattern
  - Playwright BasePage
  - POM base class
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/framework-design
date_created: 2026-07-28
mastery_level: 1
---

# BasePage Abstraction Pattern in Playwright

> **TL;DR:** `BasePage` is the foundation class for the Page Object Model—encapsulating common browser operations (`navigate`, `waitForURL`, `takeScreenshot`) so individual page classes inherit clean, reusable UI helper methods without duplicating page interactions!

---

## Why This Exists
In large E2E automation suites, repetitive tasks like waiting for page loads, handling toast notifications, scrolling, and taking failure screenshots pollute page objects. `BasePage` acts as a common parent class providing shared helper utilities across all page objects.

---

## Canonical Code Example

```javascript
/**
 * BasePage class providing reusable page interaction wrappers.
 */

export class BasePage {
  /**
   * @param {import('@playwright/test').Page} page
   */
  constructor(page) {
    this.page = page;
  }

  async navigateTo(path) {
    await this.page.goto(path, { waitUntil: 'domcontentloaded' });
  }

  async getTitle() {
    return await this.page.title();
  }

  async captureScreenshot(name) {
    await this.page.screenshot({ path: `screenshots/${name}.png`, fullPage: true });
  }
}
```

---

## Related
- [[pw-page-object-model-pattern]] — Page Object Model architecture.
- [[pw-custom-fixtures]] — Injecting Page Objects via custom fixtures.
- [[MOC - Playwright Framework Design]] — Playwright Framework MOC.
