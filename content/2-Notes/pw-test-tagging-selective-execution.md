---
id: 202608152104
title: Test Tagging and Selective CLI Execution
aliases:
  - test tagging
  - grep flag
  - grep-invert flag
  - selective test execution
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/execution
date_created: 2026-08-15
mastery_level: 1
---

# Test Tagging and Selective CLI Execution

---

> **TL;DR:** Appending `@tag` annotations (e.g., `@web`, `@api`, `@smoke`) to test titles enables selective execution via `npx playwright test --grep "@smoke"` or excluding tests via `--grep-invert "@flaky"`.

---

## Why This Exists

Running a full test suite of 500 specs on every minor pull request takes too long. Developers need to trigger specific subsets of tests (like running only `@smoke` tests on PR check-ins or running only `@api` tests during backend deployment builds). Playwright provides native **Test Tagging** and command-line filtering via `--grep` and `--grep-invert`.

---

## Mental Model

Imagine sorting mail into tagged office mailboxes.
- **Untagged Suite Execution:** Delivering every letter in the mailbag to every desk in the building regardless of recipient.
- **Tagged CLI Filtering (`--grep "@smoke"`):** A sorting room clerk placing a colored sticker (`@smoke`, `@api`, `@regression`) on each letter title. When the clerk triggers `--grep "@smoke"`, the conveyor belt routes strictly the letters carrying the green `@smoke` sticker.

---

## How It Works

1. **Tag Test Titles or Blocks:**  
   Embed `@tag` annotations directly inside `test()` title strings or options:
   ```javascript
   test('user checkout flow @web @smoke', async ({ page }) => { ... });
   test('create product API endpoint @api', async ({ request }) => { ... });
   ```
   Or use the `tag` option property:
   ```javascript
   test('login test', { tag: ['@web', '@smoke'] }, async ({ page }) => { ... });
   ```
2. **Execute Tagged Subsets via CLI:**
   - **Run Only Web Tests:** `npx playwright test --grep "@web"`
   - **Run Only API Tests:** `npx playwright test --grep "@api"`
   - **Run Combined Tags (OR logic):** `npx playwright test --grep "@smoke|@critical"`
   - **Run Combined Tags (AND logic):** `npx playwright test --grep "(?=.*@web)(?=.*@smoke)"`
   - **Exclude Specific Tags:** `npx playwright test --grep-invert "@flaky"`

---

## Key Characteristics

- **Zero Structural Code Changes:** Tags are simple string annotations inside test names or metadata options.
- **Regex Pattern Matching:** Supports regular expressions for complex logical AND/OR matching.
- **CI/CD Integration:** Pass tags dynamically from CI pipeline environment variables or parameter dropdowns.

---

## Common Mistakes

- **Forgetting the `@` symbol in tag names:** Naming a tag `smoke` instead of `@smoke`. Running `--grep "smoke"` will match any test title containing the word "smoke" (e.g., `"verify smoke animation"`).
- **Matching unexpected test titles with short grep keywords:** Using `--grep "web"` which accidentally matches titles like `"webcam test"`. Always prefix custom tags with `@` (e.g., `@web`).

---

## Canonical Code Example

```javascript
// Test spec demonstrating inline tag annotations and metadata tags
const { test, expect } = require('@playwright/test');

// 1. Inline title tagging (@web and @smoke)
test('verify home page navigation @web @smoke', async ({ page }) => {
  await page.goto('https://example.com');
  await expect(page.locator('h1')).toBeVisible();
});

// 2. Metadata option array tagging
test('create product via REST API', { tag: ['@api', '@regression'] }, async ({ request }) => {
  const response = await request.post('https://example.com/api/products', {
    data: { name: 'Test Item' },
  });
  expect(response.status()).toBe(201);
});
```

### CLI Command Execution Commands

```bash
# Run only tests tagged with @web
npx playwright test --grep "@web"

# Run only tests tagged with @smoke
npx playwright test --grep "@smoke"

# Exclude tests tagged with @flaky
npx playwright test --grep-invert "@flaky"
```

---

## Key Takeaways

- Annotate test titles with `@tag` strings (e.g., `@web`, `@api`, `@smoke`).
- Filter execution using `npx playwright test --grep "@tag"`.
- Exclude problematic tests using `npx playwright test --grep-invert "@flaky"`.

---

## Related

- [[node-custom-npm-scripts-automation]] — Driving CLI flags via npm scripts
- [[jenkins-ci-playwright-integration]] — Parameterized CI execution
- [[MOC - Playwright Execution]]
