---
id: 202608152110
title: Cucumber BDD Framework Architecture and Gherkin Feature Files
aliases:
  - cucumber bdd
  - gherkin syntax
  - feature files
  - Given When Then
tags:
  - type/concept
  - status/processing
  - tool/cucumber
  - tool/playwright
  - topic/architecture
date_created: 2026-08-15
mastery_level: 1
---

# Cucumber BDD Framework Architecture and Gherkin Feature Files

---

> **TL;DR:** Cucumber BDD wraps automated test automation in human-readable `.feature` files written in Gherkin syntax (`Feature`, `Scenario`, `Given`, `When`, `Then`)—bridging communication between business stakeholders and QA engineers.

---

## Why This Exists

Complex test spec code (`await page.locator('#btn-4').click()`) is unreadable to product managers, business analysts, and non-technical stakeholders. **Behavior-Driven Development (BDD)** via Cucumber introduces plain-English specification files (`.feature`) written in Gherkin syntax. This allows stakeholders to define business requirements directly as executable test scenarios.

---

## Mental Model

Imagine a restaurant dining menu.
- **Underlying Code (`Step Definitions`):** The kitchen recipe describing exact oven temperatures, knife cutting angles, and seasoning weights.
- **Gherkin Feature File (`.feature` Menu):** The customer-facing menu written in plain English (`Given a hungry customer, When they order the ZARA Burger, Then the waiter serves the burger`). Customers understand the menu instantly, while the chef executes the recipe in the kitchen.

---

## Gherkin Keyword Architecture

```text
Feature: [High-Level Business Functionality Name]
  Description of business goals

  Scenario: [Specific User Action Journey / Test Case Name]
    Given [Initial context or precondition state]
    When  [Action or event performed by the user]
    Then  [Expected outcome or assertion validation]
```

### Keyword Definitions
- **`Feature`:** High-level test suite module description (e.g., `Feature: E-Commerce Product Checkout`).
- **`Scenario`:** Individual test case scenario.
- **`Given`:** Preconditions or initial context (`Given user navigates to login page`).
- **`When`:** Action steps (`When user enters valid credentials and clicks login`).
- **`Then`:** Assertion validation steps (`Then user should see the dashboard header`).
- **`And` / `But`:** Syntactic sugar for chaining multiple `Given`, `When`, or `Then` statements.

---

## Key Characteristics

- **Human-Readable Specifications:** Non-technical product owners can write and review executable specifications.
- **Data Parameterization in Quotes:** Pass dynamic test values in double quotes (`Given user logs in with "user@test.com" and "pass123"`).
- **Separation of Specs from Code:** `.feature` text files are decoupled from underlying JavaScript automation scripts.

---

## Common Mistakes

- **Writing technical code locators inside Gherkin feature files:** Writing `When I click on element "#submit-btn-id"`. Gherkin files must express business behavior (`When I submit the order form`), keeping technical DOM details inside step definition files!
- **Placing `.feature` files outside the designated `features/` directory:** Saving feature files in root directories where Cucumber CLI cannot locate them.

---

## Canonical Feature File Example (`features/ecommerce.feature`)

```gherkin
# Gherkin Feature Specification File
Feature: E-Commerce Order Placement and Verification

  Scenario: Successfully place product order and verify in order history
    Given I log in to e-commerce application with "anshika@gmail.com" and "Iamking@123"
    When I add product "ZARA COAT 3" to the cart
    And I enter valid shipping details for "India" and place the order
    Then I verify the order confirmation ID is displayed in the order history page
```

---

## Key Takeaways

- Cucumber BDD uses plain-English `.feature` files written in Gherkin syntax.
- Use `Given` (precondition), `When` (action), and `Then` (assertion validation).
- Decouples business logic specifications from underlying Playwright code mechanics.

---

## Related

- [[cucumber-step-definitions-browser-lifecycle]] — Step definition implementation
- [[cucumber-world-context-lifecycle-hooks]] — Cucumber World context and hooks
- [[MOC - Playwright Framework Design]]
