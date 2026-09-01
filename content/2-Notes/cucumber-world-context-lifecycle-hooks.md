---
id: 202608152112
title: Cucumber World Context and Lifecycle Hooks
aliases:
  - cucumber world
  - cucumber hooks
  - Before After hooks
  - this.attach screenshot
tags:
  - type/concept
  - status/processing
  - tool/cucumber
  - tool/playwright
  - topic/architecture
date_created: 2026-08-15
mastery_level: 1
---

# Cucumber World Context and Lifecycle Hooks

---

> **TL;DR:** Cucumber's **World Context** (`this`) shares state (like `this.page` or order IDs) across different step functions in a scenario, while `Before`, `After`, and `BeforeStep` lifecycle hooks handle browser instantiation and screenshot attachments on failure.

---

## Why This Exists

Step definitions in Cucumber are isolated functions. Passing variables (like a generated `orderId` or the active `page` instance) from a `Given` step function to a `Then` step function without global scope pollution requires a shared context object. Cucumber provides the **World Context** accessible via `this` inside every step function, alongside `Before` and `After` hooks for browser setup and teardown.

---

## Mental Model

Imagine a relay race team.
- **Isolated Step Functions:** Each runner operates independently. If Runner #1 finishes their lap with a baton (`orderId`), but throws it into the grass, Runner #2 has nothing to receive.
- **World Context (`this` Baton):** A official team belt worn by all runners. Runner #1 clips the baton to the belt (`this.orderId = '123'`). When Runner #2 takes over for the next lap (`When step`), they unclip the baton from the same belt (`this.orderId`) and keep running.

---

## Architecture Flow

```text
┌─────────────────────────────────────────────────────────────┐
│                       Before Hook                           │
│  - Launch Browser: this.browser = await chromium.launch()   │
│  - Create Page:    this.page = await context.newPage()      │
└─────────────────────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                    Given Step Function                      │
│  - Access shared page: await this.page.goto(...)            │
│  - Store state:        this.orderId = '999'                 │
└─────────────────────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                    Then Step Function                       │
│  - Read shared state:  console.log(this.orderId)            │
│  - Perform assertion:  expect(this.page...).toBeVisible()   │
└─────────────────────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                       After Hook                            │
│  - Check status:   if (result.status === Status.FAILED)     │
│  - Attach image:   await this.attach(pngBuffer, 'image/png')│
│  - Close Browser:  await this.browser.close()               │
└─────────────────────────────────────────────────────────────┘
```

---

## How It Works

### Lifecycle Hooks (`features/support/hooks.js`)
```javascript
const { Before, After, BeforeStep, AfterStep, Status } = require('@cucumber/cucumber');
const { chromium } = require('@playwright/test');

Before(async function () {
  this.browser = await chromium.launch({ headless: true });
  this.context = await this.browser.newContext();
  this.page = await this.context.newPage();
});

After(async function (scenario) {
  // Capture failure screenshot and attach to Cucumber HTML report
  if (scenario.result.status === Status.FAILED) {
    const buffer = await this.page.screenshot({ path: `screenshots/${scenario.pickle.name}.png` });
    await this.attach(buffer, 'image/png');
  }
  await this.browser.close();
});
```

---

## Key Characteristics

- **Shared State (`this`):** Attach custom properties (`this.page`, `this.poManager`) to access them across all steps in the current scenario.
- **Fresh Isolated Context:** A new World instance and `Before` hook executes for every scenario, guaranteeing test isolation.
- **Embedded Failure Attachments:** `this.attach(buffer, 'image/png')` embeds screenshots directly inside Cucumber HTML reports.

---

## Common Mistakes

- **Using arrow functions `() => {}` in hooks:** Writing `Before(async () => { ... })`. Arrow functions bind `this` to the outer lexical context, breaking the World instance! Always use `async function()`.
- **Forgetting to close browser contexts in `After` hooks:** Omitting `await this.browser.close()`, leaving zombie browser processes running in system memory after test runs.

---

## Canonical Code Example

```javascript
// Cucumber hooks managing Playwright browser lifecycle and failure screenshots
const { Before, After, Status } = require('@cucumber/cucumber');
const { chromium } = require('@playwright/test');
const { POManager } = require('../../pageObjects/POManager');

Before(async function () {
  // 1. Initialize Playwright browser and page in World context
  this.browser = await chromium.launch({ headless: true });
  this.context = await this.browser.newContext();
  this.page = await this.context.newPage();

  // 2. Initialize Page Object Manager on World context
  this.poManager = new POManager(this.page);
});

After(async function (scenario) {
  // 3. Attach PNG screenshot to report if scenario fails
  if (scenario.result.status === Status.FAILED) {
    const screenshotBuffer = await this.page.screenshot();
    await this.attach(screenshotBuffer, 'image/png');
  }

  // 4. Teardown browser instance
  await this.browser.close();
});
```

---

## Key Takeaways

- Access shared scenario variables across step functions using `this` (World context).
- Use `Before` and `After` hooks for browser lifecycle management.
- Embed failure screenshots into Cucumber reports using `await this.attach(buffer, 'image/png')`.

---

## Related

- [[cucumber-step-definitions-browser-lifecycle]] — Step definition mapping
- [[cucumber-bdd-gherkin-feature-architecture]] — Gherkin feature architecture
- [[MOC - Playwright Framework Design]]
