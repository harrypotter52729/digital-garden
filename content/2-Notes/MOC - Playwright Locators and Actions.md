---
id: 202606131230
title: MOC - Playwright Locators and Actions
aliases:
  - Playwright Locators MOC
  - Locators MOC
tags:
  - type/MOC
  - tool/playwright
  - status/processing
  - topic/locating-interacting
date_created: 2026-06-13
mastery_level: 1
---

# MOC - Playwright Locators and Actions

This map acts as a central hub for understanding element targeting, actionability checks, auto-waiting behaviors, and handling complex page contexts like iframes, multiple tabs, and native JavaScript dialogs.

## Selector Strategies
* [[pw-built-in-locators-vs-css-xpath]] - Navigating semantic, user-facing accessible roles vs structural code layouts.

## Waiting Mechanics
* [[pw-actionability-and-auto-waiting]] - How Playwright prevents flakiness by verifying element state automatically before actions.
* [[pw-handling-custom-wait-conditions]] - Monitoring window parameter updates and polling container element state.

## Complex Contexts
* [[pw-handling-iframes-and-multiple-tabs]] - Traversing embedded document contexts and managing multiple active pages.
* [[pw-handling-alerts-and-dialogs]] - Managing blocking JavaScript popups without halting thread execution.

---
**Related MOCs:**
* [[MOC - Playwright Architecture]]
* [[MOC - JavaScript Core]]
