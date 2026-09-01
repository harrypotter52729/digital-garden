---
id: 202608152114
title: Cucumber HTML Reporting and Automated Retry Integration
aliases:
  - cucumber html report
  - cucumber retry
  - --format html:report.html
  - --retry flag
tags:
  - type/concept
  - status/processing
  - tool/cucumber
  - tool/playwright
  - topic/debugging
date_created: 2026-08-15
mastery_level: 1
---

# Cucumber HTML Reporting and Automated Retry Integration

---

> **TL;DR:** Passing `--format html:cucumber-report.html` and `--retry 1` flags to the `cucumber-js` CLI generates standalone HTML reports and automatically re-executes failing scenarios to filter out transient infrastructure flakiness.

---

## Why This Exists

Running Cucumber test suites via terminal commands outputs text logs, but provides no visual pass/fail summary for stakeholders. Furthermore, transient network glitches can cause scenarios to fail on their initial run. Cucumber provides command-line flags (`--format html:path.html` and `--retry N`) to publish HTML test reports and automatically re-run failing scenarios.

---

## Mental Model

Imagine a school grading system.
- **Raw Terminal Output:** The teacher shouting student scores out loud in a hallway. Nobody has a written record of who passed or failed.
- **HTML Reporting & Retries (`--format html` + `--retry 1`):** The teacher generating a printed color report card (`--format html:report.html`). If a student fumbles Question #1 on the first try, the teacher grants an immediate second attempt (`--retry 1`). If they pass on the second attempt, the report marks the final outcome as passed while logging the retry.

---

## How It Works

1. **HTML Report Generation:**  
   Pass the `--format` option pointing to a target `.html` output file:
   `npx cucumber-js --format html:reports/cucumber_report.html --exit`
2. **Automated Scenario Retries:**  
   Pass `--retry 1` to re-execute failing scenarios up to $N$ times:
   `npx cucumber-js --retry 1 --exit`
3. **Combined CLI Execution String:**
   `npx cucumber-js --tags "@regression" --retry 1 --format html:reports/cucumber_report.html --exit`

---

## Key Characteristics

- **Standalone HTML Output:** Produces single-file self-contained HTML reports featuring green pass checks, red failure blocks, and expandable error logs.
- **Embedded Failure Attachments:** Embedded screenshots (`this.attach(buffer, 'image/png')`) render directly inside failed scenario step nodes in the HTML report.
- **Automated Flaky Resolution:** Scenarios that fail on run 1 but pass on retry 1 log as passed in final HTML reports.

---

## Common Mistakes

- **Forgetting the `--exit` flag:** Executing `npx cucumber-js --format html:report.html` without `--exit`. Node processes hang indefinitely in the terminal after tests finish!
- **Overwriting report filenames without timestamps in CI:** Using a static path `report.html` across 10 CI pipeline builds, causing old build reports to be overwritten by new runs. Use dynamic environment timestamp names or clean output folders.

---

## Canonical npm Script Configuration (`package.json`)

```json
{
  "name": "playwright-cucumber-framework",
  "version": "1.0.0",
  "scripts": {
    "test:cucumber": "npx cucumber-js --exit",
    "test:cucumber:regression": "npx cucumber-js --tags \"@regression\" --retry 1 --format html:reports/cucumber_report.html --exit",
    "test:cucumber:parallel": "npx cucumber-js --parallel 2 --format html:reports/parallel_report.html --exit"
  }
}
```

### CLI Command Execution

```bash
# Execute Cucumber regression suite with 1 retry and HTML report output
npm run test:cucumber:regression
```

---

## Key Takeaways

- Generate HTML reports using `npx cucumber-js --format html:reports/cucumber_report.html`.
- Re-run failing scenarios automatically using `--retry 1`.
- Always append `--exit` to terminate `cucumber-js` Node processes cleanly.

---

## Related

- [[cucumber-world-context-lifecycle-hooks]] — Capturing screenshots in hooks
- [[node-custom-npm-scripts-automation]] — npm script automation
- [[pw-flaky-test-retries-detection]] — Playwright native retries
