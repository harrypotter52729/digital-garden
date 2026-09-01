---
id: 202608152119
title: Playwright Inbuilt Autonomous AI Agents: Planner, Generator, and Healer
aliases:
  - Playwright AI agents
  - Planner agent
  - Generator agent
  - Healer agent
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - tool/mcp
  - topic/architecture
date_created: 2026-08-15
mastery_level: 1
---

# Playwright Inbuilt Autonomous AI Agents: Planner, Generator, and Healer

---

> **TL;DR:** Playwright provides three specialized autonomous AI agents—**Planner** (explores web apps and drafts Markdown test plans), **Generator** (converts test plans into executable Playwright test specs), and **Healer** (diagnoses and auto-repairs broken selectors in failing scripts).

---

## Why This Exists

Creating complete test automation suites from scratch requires three labor-intensive phases: (1) manually exploring web applications to write test plans, (2) drafting Playwright spec code for every scenario, and (3) constantly updating broken element selectors when web developers update UI layouts. Playwright introduces three coupled autonomous agents—**Planner**, **Generator**, and **Healer**—to automate this end-to-end lifecycle.

---

## Mental Model

Imagine an automated software development pipeline team.
1. **Planner Agent (The Test Architect):** Navigates through a new web application, discovers all product search forms, checkout flows, and edge cases, and writes a comprehensive Markdown specification document (`test_plan.md`).
2. **Generator Agent (The Developer):** Reads `test_plan.md`, launches a live browser instance, inspects DOM elements, and writes resilient Playwright test specs (`search.spec.ts`).
3. **Healer Agent (The Maintenance Engineer):** Observes test suite runs. When a test fails due to a changed button ID, Healer inspects the live page, discovers the updated DOM structure, fixes the locator string in code, and verifies that the test passes.

---

## Agent Triad Architecture

```text
┌───────────────────────────────────────┐
│           1. PLANNER AGENT            │
│  - Navigates live web application     │
│  - Identifies user flows & edge cases │
│  - Outputs markdown `test_plan.md`    │
└───────────────────────────────────────┘
                    │
                    ▼
┌───────────────────────────────────────┐
│          2. GENERATOR AGENT           │
│  - Reads `test_plan.md` context       │
│  - Generates executable Playwright    │
│    test scripts (`*.spec.ts`)         │
└───────────────────────────────────────┘
                    │
                    ▼
┌───────────────────────────────────────┐
│           3. HEALER AGENT             │
│  - Monitors failing spec runs         │
│  - Diagnoses strict mode & locator    │
│    violations                         │
│  - Auto-repairs spec code             │
└───────────────────────────────────────┘
```

---

## How It Works

1. **Setup Agent Environment:**  
   Initialize Playwright agent chatmodes in VS Code:
   `npx @playwright/agent-setup`
2. **Phase 1: Run Planner Agent:**  
   Select `@planner` chatmode and prompt:
   `Explore https://example.com/shop and generate a comprehensive markdown test plan for search and checkout functionality.`
3. **Phase 2: Run Generator Agent:**  
   Select `@generator` chatmode, attach `test_plan.md` as context, and prompt:
   `Generate Playwright test scripts in Page Object standards for Section 2.1 Product Search.`
4. **Phase 3: Run Healer Agent:**  
   Select `@healer` chatmode if a generated test fails:
   `Diagnose and repair the failing test search_no_results.spec.ts.`

---

## Key Characteristics

- **Zero-Boilerplate Test Planning:** Planner agent discovers edge cases (special characters, empty search results, boundary conditions) automatically.
- **Resilient Script Generation:** Generator agent uses standard Playwright user-facing locators (`getByRole`, `getByText`).
- **Self-Healing Test Maintenance:** Healer agent resolves strict mode violations (`locator resolved to 2 elements`) and locator ID shifts automatically.

---

## Common Mistakes

- **Expecting 100% human-free automation:** Assuming AI agents can replace human oversight completely. Always review generated test plans (`test_plan.md`) and verify generated code logic before committing to production repositories.
- **Running Healer agent without seed files on authenticated pages:** Attempting to heal a spec on page 5 of a complex multi-step checkout without providing a seed script to log in first.

---

## Canonical Agent Prompting Workflow

### 1. Planner Agent Prompt (`@planner`)

```text
Prompt: @planner Explore https://rahulshettyacademy.com/seleniumPractise/#/ and generate a detailed Markdown test plan covering product search, array quantity increments, cart additions, and promo code verification.
```

### 2. Generator Agent Prompt (`@generator`)

```text
Prompt: @generator Using attached `test_plan.md` context, generate executable Playwright TypeScript test specs for Section 2: Product Search Functionality.
```

### 3. Healer Agent Prompt (`@healer`)

```text
Prompt: @healer The test `tests/search_no_results.spec.ts` failed during execution. Investigate the live page state, repair broken locator selectors, and verify execution passes.
```

---

## Key Takeaways

- **Planner** explores applications and drafts structured Markdown test plans.
- **Generator** reads test plans and generates Playwright test scripts.
- **Healer** investigates test failure stack traces and auto-repairs broken locator code.

---

## Related

- [[mcp-playwright-ai-agent-integration]] — Model Context Protocol integration
- [[github-copilot-playwright-ai-integration]] — GitHub Copilot AI integration
- [[MOC - Playwright Architecture]]
