---
id: 202608152113
title: Cucumber Scenario Parameterization and Tagged CLI Execution
aliases:
  - Scenario Outline
  - Examples table
  - cucumber tags
  - cucumber CLI tagging
tags:
  - type/concept
  - status/processing
  - tool/cucumber
  - tool/playwright
  - topic/execution
date_created: 2026-08-15
mastery_level: 1
---

# Cucumber Scenario Parameterization and Tagged CLI Execution

---

> **TL;DR:** `Scenario Outline:` paired with an `Examples:` table iterates the same Gherkin scenario across multiple data rows using `<placeholder>` syntax, while `@tag` annotations enable selective execution via `npx cucumber-js --tags "@regression"`.

---

## Why This Exists

Hardcoding test data directly inside Gherkin feature files (`Given I log in with "user1@test.com"`) requires duplicating entire scenario blocks when testing multiple user roles. Cucumber provides **Scenario Outlines** to parameterize scenarios using data tables (`Examples:`). Additionally, assigning `@tag` annotations (`@smoke`, `@regression`, `@web`) enables QA engineers to run selective subsets of Cucumber feature files.

---

## Mental Model

Imagine a mail merge template for printing certificates.
- **Hardcoded Gherkin Scenario:** Manually typing out 50 separate certificate documents in Microsoft Word, re-typing the student's name by hand on every single document.
- **Scenario Outline + Examples Table:** Creating one master certificate template (`Scenario Outline:`) with placeholder tags (`<StudentName>`, `<Course>`). The mail merge engine (`Examples:` table) reads 50 rows from a spreadsheet and prints 50 customized certificates automatically.

---

## How It Works

1. **Scenario Outline Syntax (`.feature`):**  
   Use `Scenario Outline:` and enclose parameter variables in angle brackets `<variable>`:
   ```gherkin
   @validation
   Scenario Outline: Verify user login failure with invalid credentials
     Given I navigate to the login page
     When I enter invalid username "<username>" and password "<password>"
     Then I should see error message "Incorrect email or password."

     Examples:
       | username          | password     |
       | user1@badtest.com | WrongPass1   |
       | user2@badtest.com | WrongPass2   |
   ```
2. **Selective Tagged CLI Execution:**
   - **Run Only Tagged Scenarios:** `npx cucumber-js --tags "@validation"`
   - **Run Combined Tags (AND):** `npx cucumber-js --tags "@smoke and @web"`
   - **Exclude Specific Tags:** `npx cucumber-js --tags "not @flaky"`

---

## Key Characteristics

- **Data Table Iteration:** Automatically re-runs the entire scenario for every row in the `Examples:` table.
- **Flexible Tag Filtering:** Filter by single tag (`@smoke`), logical AND (`@web and @smoke`), or exclusion (`not @slow`).
- **Clean Feature Readability:** Keeps test data cleanly structured in formatted Markdown-like tables.

---

## Common Mistakes

- **Using `Scenario:` instead of `Scenario Outline:` for Examples tables:** Writing `Scenario:` with an `Examples:` table underneath. Cucumber ignores the `Examples:` table unless the header explicitly reads `Scenario Outline:`!
- **Mismatched header names in `Examples:` tables:** Writing `<user>` in the Gherkin step, but defining `| username |` in the table header. Placeholder strings must match table header column names exactly!

---

## Canonical Code Example (`features/login_outline.feature`)

```gherkin
# Parameterized Cucumber Scenario Outline with Examples Table
Feature: Account Login Security Validation

  @smoke @validation
  Scenario Outline: Verify system handles various login credential combinations
    Given I log in to e-commerce application with "<email>" and "<password>"
    Then I verify the login outcome matches expected status "<expectedStatus>"

    Examples:
      | email             | password     | expectedStatus |
      | admin@test.com    | SecretPass1  | Success        |
      | invalid@test.com  | WrongPass    | Failure        |
      | locked@test.com   | SecretPass1  | Locked         |
```

### CLI Command Execution Commands

```bash
# Run scenarios tagged with @smoke
npx cucumber-js --tags "@smoke" --exit

# Run scenarios tagged with @validation AND @smoke
npx cucumber-js --tags "@validation and @smoke" --exit

# Exclude scenarios tagged with @flaky
npx cucumber-js --tags "not @flaky" --exit
```

---

## Key Takeaways

- Use `Scenario Outline:` with `<placeholder>` variables to parameterize Gherkin scenarios.
- Define data rows inside an `Examples:` table underneath the outline.
- Trigger tagged scenarios using `npx cucumber-js --tags "@tag"`.

---

## Related

- [[cucumber-bdd-gherkin-feature-architecture]] — Gherkin feature architecture
- [[cucumber-step-definitions-browser-lifecycle]] — Cucumber step definitions
- [[pw-data-driven-json-parameterization]] — Playwright JSON parameterization
