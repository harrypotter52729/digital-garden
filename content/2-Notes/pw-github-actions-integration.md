---
id: 202606131242
title: GitHub Actions Integration
aliases:
  - playwright-cicd
  - github-actions-playwright
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/execution-cicd
date_created: 2026-06-13
mastery_level: 1
---

# GitHub Actions Integration

> **TL;DR:** To catch bugs early, tests should run automatically on every code push or pull request. **GitHub Actions** is a continuous integration (CI) tool that spins up a clean Linux server container, installs your app, downloads the Playwright browsers, runs your test suite, and uploads diagnostic traces if tests fail.

## The Quality Control Conveyor Belt Analogy

Imagine running an automated toy factory:

```
                  [ Git Push / Pull Request ]
                              |
                              v
                  [ Conveyor Belt Starts ]
                  (GitHub Actions Workflow)
                              |
     +------------------------+------------------------+
     |                                                 |
     v                                                 v
[ Step 1: Unpack Parts ]                      [ Step 2: Assemble & Inspect ]
Installs Node.js & dependencies               Runs 'npx playwright test'
     |                                                 |
     +------------------------+------------------------+
                              |
                              v
                  [ Step 3: Package Output ]
                  If check fails, saves flight recorder (trace.zip)
                  and files report on dashboard.
```

- **The conveyor belt scheduler:** GitHub Actions (configured via a `.yml` file in your repo).
- **The inspection robots:** Playwright running headlessly (without drawing a visual window to save CPU and memory).
- **The reject bin:** GitHub Actions artifacts (saving reports and traces only when a test fails).

---

## Optimizing CI Runs

Running tests in CI can be slow because servers start from scratch. We optimize this using two caching strategies:
1. **Node Modules Cache:** Store NPM package dependencies so they don't download from the internet on every run.
2. **Playwright Browsers Cache:** Store the large browser executable binaries (Chromium, Firefox, WebKit) in GitHub's cache space to bypass download scripts.

---

## Canonical Code Example

Create this configuration file at `.github/workflows/playwright.yml` in your repository root to configure test execution on GitHub Actions:

```yaml
# .github/workflows/playwright.yml
name: Playwright Tests

# Trigger the workflow on push or pull request to main/master branches
on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]

jobs:
  test:
    timeout-minutes: 60
    runs-on: ubuntu-latest

    steps:
    # 1. Check out the repository code
    - name: Checkout repository
      uses: actions/checkout@v4

    # 2. Setup Node.js environment
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: 18
        cache: 'npm' # Caches npm packages automatically

    # 3. Install project dependencies
    - name: Install dependencies
      run: npm ci

    # 4. Cache Playwright Browsers to speed up pipeline runs
    - name: Cache Playwright Browser Binaries
      id: cache-browsers
      uses: actions/cache@v4
      with:
        path: ~/.cache/ms-playwright
        key: ${{ runner.os }}-playwright-${{ hashFiles('**/package-lock.json') }}

    # 5. Install Playwright browsers ONLY if cache misses
    - name: Install Playwright Browsers
      if: steps.cache-browsers.outputs.cache-hit != 'true'
      run: npx playwright install --with-deps

    # 6. Run tests headlessly
    # process.env.CI is automatically set to true by GitHub Actions
    - name: Run Playwright tests
      run: npx playwright test

    # 7. Upload HTML reports & Traces if tests fail
    - name: Upload Test Report Artifact
      uses: actions/upload-artifact@v4
      if: always() # Ensure report uploads even if tests fail
      with:
        name: playwright-report
        path: playwright-report/
        retention-days: 30
```

---

## Related
* [[pw-html-reporter-and-traces]] - Collecting and reading trace zip archives downloaded from GitHub run runs.
* [[pw-playwright-config-file]] - Tuning workers, retries, and environments specifically for pipeline runs.
