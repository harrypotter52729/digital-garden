---
id: 202606131225
title: MOC - Playwright Architecture
aliases:
  - Playwright Architecture MOC
  - Architecture MOC
tags:
  - type/MOC
  - tool/playwright
  - status/processing
  - topic/architecture
date_created: 2026-06-13
mastery_level: 1
---

# MOC - Playwright Architecture

This map acts as a central hub for understanding Playwright's execution model, process isolation, global configurations, and the lifecycle hooks of the test runner.

## Execution & Isolation Model
* [[pw-browser-context-and-page-model]] - Understanding the hierarchy of Browser, Browser Context, and Page, and how it prevents state leakage.

## Global Configuration
* [[pw-playwright-config-file]] - Defining global options like workers, retries, reporting, and default browser options.

## Test Execution Lifecycle
* [[pw-test-runner-and-hooks]] - Controlling setup and teardown stages via hooks (beforeAll, beforeEach, afterEach, afterAll).

---
**Related MOCs:**
* [[MOC - JavaScript Core]]


* [[pw-network-route-fulfillment]] — Network mocking & request fulfillment via `page.route()`.
