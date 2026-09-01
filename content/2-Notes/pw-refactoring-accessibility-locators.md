---
id: 202608152034
title: Refactoring Framework Tests with User-Facing Accessibility Locators
aliases:
  - refactoring to getByRole
  - accessibility refactoring
  - resilient locator strategy
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/architecture
date_created: 2026-08-15
mastery_level: 1
---

# Refactoring Framework Tests with User-Facing Accessibility Locators

---

> **TL;DR:** Refactoring test suites from fragile CSS selectors (`page.locator('.btn-action-primary-v2')`) to user-facing accessibility locators (`getByRole`, `getByLabel`, `getByPlaceholder`, `getByText`) makes test scripts highly resilient against UI re-skinning and class name changes.

---

## Why This Exists

Automated test suites built exclusively on CSS class names or XPath strings suffer from high maintenance overhead. Whenever frontend teams refactor CSS frameworks (e.g., migrating from Bootstrap to Tailwind) or update DOM structure hierarchies, brittle CSS locators break. Refactoring scripts to user-facing locators aligns test code with user perception and WCAG accessibility standards.

---

## Mental Model

Imagine writing directions to a restaurant.
- **Brittle Implementation Direction (CSS Selector):** "Walk to latitude 40.7128, longitude -74.0060, enter building #3409, take stairwell B." If the city renumbers building street addresses, the directions fail.
- **User-Facing Accessibility Direction (`getByRole`/`getByText`):** "Walk to the corner of Main Street and look for the large sign reading 'Joe's Diner'." As long as the sign exists, visitors find the restaurant regardless of building street renumbering.

---

## How It Works

### Migration Mapping Strategy

| Legacy Brittle CSS / XPath | Refactored User-Facing Accessibility Locator |
| :--- | :--- |
| `page.locator('input#username')` | `page.getByLabel('Username')` or `page.getByPlaceholder('Email')` |
| `page.locator('button.btn-submit')` | `page.getByRole('button', { name: 'Submit' })` |
| `page.locator('a[href="/cart"]')` | `page.getByRole('link', { name: 'Cart' })` |
| `page.locator('.alert-success')` | `page.getByText('Order placed successfully')` |
| `page.locator('.card-body').nth(2)` | `page.locator('.card').filter({ hasText: 'Nokia' })` |

---

## Key Characteristics

- **High Resiliency:** Withstands major frontend CSS framework rewrites without breaking tests.
- **Readable Spec Code:** Tests read like plain-English descriptions of user interactions.
- **Implicit Accessibility Audit:** Identifies inaccessible UI controls missing proper labels or ARIA attributes during test migration.

---

## Common Mistakes

- **Forgetting that `getByRole` is case-sensitive or requires exact name matching:** Passing `getByRole('button', { name: 'submit' })` when the button text is capitalized `'Submit'`. Use RegExp `/submit/i` for case-insensitive matching!
- **Overusing `getByText()` on generic non-unique text:** Using `getByText('Add')` when multiple buttons on screen contain the word "Add". Combine with `.filter()` or `getByRole()`.

---

## Code Refactoring Before & After

### Before Refactoring (Brittle CSS Selectors)

```javascript
// BRITTLE: Depends on internal implementation CSS classes and element tags
test('checkout flow with CSS selectors', async ({ page }) => {
  await page.goto('https://example.com/checkout');

  await page.locator('input#user-email-field').fill('user@test.com');
  await page.locator('button.action-submit-btn-v2').click();
  await page.locator('div.alert-box-success').waitFor();

  expect(await page.locator('div.alert-box-success').textContent()).toContain('Thank you');
});
```

### After Refactoring (Resilient Accessibility Locators)

```javascript
// RESILIENT: Refactored using Playwright user-facing accessibility locators
test('checkout flow with accessibility locators', async ({ page }) => {
  await page.goto('https://example.com/checkout');

  await page.getByPlaceholder('Select Country').pressSequentially('India', { delay: 100 });
  await page.getByRole('button', { name: 'Place Order' }).click();

  // Web-first assertion using getByText
  await expect(page.getByText('Thank you for the order.')).toBeVisible();
});
```

---

## Key Takeaways

- Refactor brittle CSS selectors to `getByRole`, `getByLabel`, `getByPlaceholder`, and `getByText`.
- User-facing locators decouple test logic from backend CSS class names.
- Makes test scripts readable, resilient, and aligned with accessibility standards.

---

## Related

- [[pw-get-by-role-accessible-locators]] — ARIA role locators
- [[pw-user-facing-locators-get-by-label]] — Form label locators
- [[MOC - Playwright Framework Design]]
