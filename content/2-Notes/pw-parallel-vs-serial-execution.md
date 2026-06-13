---
id: 202606131240
title: Parallel vs Serial Execution
aliases:
  - playwright-parallelism
  - test-workers
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/execution-cicd
date_created: 2026-06-13
mastery_level: 1
---

# Parallel vs Serial Execution

> **TL;DR:** By default, Playwright runs tests in **parallel** using multiple independent worker processes to speed up your test suite. However, when tests have sequential dependencies—where Test B requires changes made in Test A to execute—you can configure Playwright to run tests **serially** within a specific file.

## The Painting Analogy

Imagine remodeling a house:

```
Parallel Execution (Independent Tasks):
Painting Room 1, Painting Room 2, and Painting Room 3.
- Assign 3 painters (Worker Processes) to work at the same time.
- They finish in 1 hour total.
- No painter gets in the way of another (Complete Isolation).

Serial Execution (Sequential Dependencies):
Step 1: Lay the floorboards -> Step 2: Varnish the wood -> Step 3: Lay down the rug.
- 1 worker must perform these steps one after the other in order.
- Laying the rug first (before the floor exists) results in a crash.
- If Step 1 fails, there is no point performing Step 2 or 3.
```

In automated testing:
- **Parallel Mode:** Use this for 95% of your tests (e.g., checking search pages, checking profile details). Because they run in isolation via browser contexts, they run safely at the same time.
- **Serial Mode:** Use this for complex multi-stage flows (e.g., Test 1: Create a User account; Test 2: Modify that User's settings; Test 3: Delete the User). If Test 1 fails, Playwright skips Tests 2 and 3 automatically to save time.

---

## Workers and Parallelism Levels

Playwright scales execution at two levels:

1. **Between Files:** Playwright always runs test files in parallel. If you have 10 test files and 4 workers, Playwright spins up 4 OS processes, running 4 test files at a time.
2. **Within a Single File:** By default, tests inside a single file run sequentially on a single worker. You can enable intra-file parallelism by setting `fullyParallel: true` in your config, which distributes tests inside the same file to different workers.

---

## Canonical Code Example

This file illustrates how to declare a sequential (serial) test suite where later tests rely on the success of prior tests, contrasted with standard parallel tests.

```javascript
// @ts-check
const { test, expect } = require("@playwright/test");

// --- 1. Serial Suite: Interdependent Steps ---
test.describe.serial("CRUD Database Record Flow", () => {
  let createdRecordId;

  // If any test in this serial block fails, all subsequent tests are skipped.
  // This prevents cascading failures from wasting test execution time.

  test("Step 1: Create record in database", async ({ page }) => {
    await page.goto("/records/new");
    await page.fill("#record-name", "Telemetry Log");
    await page.click("#save-record");
    
    const successAlert = page.locator(".success-message");
    await expect(successAlert).toBeVisible();
    
    // Extract ID to use in downstream tests
    const rawIdText = await page.locator("#record-id").innerText();
    createdRecordId = rawIdText.split(":")[1].trim();
  });

  test("Step 2: Read and edit the created record", async ({ page }) => {
    // Assert the dependency exists
    expect(createdRecordId).toBeDefined();

    await page.goto(`/records/edit/${createdRecordId}`);
    await page.fill("#record-status", "Processed");
    await page.click("#save-record");
    
    await expect(page.locator(".status-badge")).toHaveText("Processed");
  });

  test("Step 3: Delete the created record", async ({ page }) => {
    expect(createdRecordId).toBeDefined();

    await page.goto(`/records/view/${createdRecordId}`);
    
    // Intercept delete confirmation dialog
    page.once("dialog", dialog => dialog.accept());
    await page.click("#delete-btn");

    await expect(page).toHaveURL("/records/list");
  });
});

// --- 2. Parallel Suite: Independent Checks ---
// These checks do not share state and run concurrently
test.describe("Independent Dashboard Audits", () => {
  // Configures tests in this block to run in parallel, even if fullyParallel is false globally
  test.describe.configure({ mode: "parallel" });

  test("Verify header visibility", async ({ page }) => {
    await page.goto("/dashboard");
    await expect(page.locator("header")).toBeVisible();
  });

  test("Verify footer visibility", async ({ page }) => {
    await page.goto("/dashboard");
    await expect(page.locator("footer")).toBeVisible();
  });
});
```

---

## Related
* [[pw-browser-context-and-page-model]] - Explains context-level cookie isolation enabling concurrent worker execution.
* [[pw-playwright-config-file]] - Setting the global worker count and `fullyParallel` configurations.
* [[js-the-event-loop-and-call-stack]] - Coordinates concurrent worker tasks using asynchronous callback loops.
