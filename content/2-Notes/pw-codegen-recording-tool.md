---
id: 202608152021
title: Code Generation and Recording with npx playwright codegen
aliases:
  - codegen
  - record and playback
  - playwright generator
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/execution
date_created: 2026-08-15
mastery_level: 1
---

# Code Generation and Recording with npx playwright codegen

---

> **TL;DR:** `npx playwright codegen` is an automated court reporter for your browser—it watches your manual clicks and keyboard inputs on a web page and writes valid Playwright test code in real time.

---

## Why This Exists

Writing element locators and test boilerplate code from scratch for simple web forms can be tedious. Playwright provides a built-in code generator tool (`npx playwright codegen`) that records manual browser interactions and generates ready-to-run test scripts in JavaScript, TypeScript, Python, Java, or C#.

---

## Mental Model

Imagine dictating a letter to a stenographer. As you speak out loud ("Dear Sir, I am writing to inquire..."), the stenographer types matching text onto paper. With `codegen`, as you perform actions on the browser screen (clicking `#login`, typing `user`), Playwright records matching API statements (`await page.click('#login')`, `await page.fill('#user', 'user')`) in the adjacent Inspector window.

---

## How It Works

1. **Launch Codegen:** Run `npx playwright codegen <url>` in your terminal.
2. **Dual Window Launch:**
   - **Browser Window:** Displays the target web application.
   - **Playwright Inspector Window:** Displays real-time generated code output.
3. **Record Actions:** Perform clicks, text inputs, dropdown selections, and checkbox checks.
4. **Assert State:** Click the "Assert visibility" or "Assert text" buttons in the Inspector toolbar to record web-first assertions.
5. **Language Selection:** Switch target code output language dropdown (JS, TS, Python, Java, C#).

---

## Key Characteristics

- **Multi-Language Generation:** Generates code for JavaScript, TypeScript, Python, Java, and C# on the fly.
- **Smart Locator Preference:** Prioritizes resilient user-facing locators (`getByRole`, `getByText`, `getByTestId`) over fragile XPath or long CSS chains.
- **Viewport & Device Emulation:** Record mobile viewport tests using device emulation flags (`--device="iPhone 13"`).

---

## Common Mistakes

- **Relying on `codegen` as a substitute for framework architecture:** Expecting generated scripts to handle dynamic data loops, Page Object Model abstractions, or complex conditional logic automatically. `codegen` generates flat linear scripts.
- **Committing raw generated code without refactoring:** Using generated linear scripts directly without organizing locators or parameterizing credentials.

---

## Practical Command Examples

```bash
# 1. Record code for a specific target URL
npx playwright codegen https://example.com/login

# 2. Record code with mobile device emulation (e.g., iPhone 13)
npx playwright codegen --device="iPhone 13" https://example.com

# 3. Record code preserving authenticated storage state
npx playwright codegen --load-storage=auth.json https://example.com/dashboard
```

---

## Key Takeaways

- `npx playwright codegen` generates Playwright test scripts by recording manual browser interactions.
- Useful for rapidly discovering resilient locators and prototyping basic user flows.
- Always refactor linear generated code into structured, modular test frameworks.

---

## Related

- [[pw-step-debugging-inspector]] — Interactive step debugging
- [[pw-project-initialization]] — Project structure setup
- [[MOC - Playwright Execution]]
