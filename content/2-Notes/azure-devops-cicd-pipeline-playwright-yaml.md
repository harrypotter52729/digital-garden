---
id: 202608152116
title: Azure DevOps CI/CD Pipelines with Playwright YAML Configuration
aliases:
  - Azure DevOps pipeline
  - azure-pipelines.yml
  - AzureCLI task
  - PublishPipelineArtifact
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - tool/azure
  - topic/environment
date_created: 2026-08-15
mastery_level: 1
---

# Azure DevOps CI/CD Pipelines with Playwright YAML Configuration

---

> **TL;DR:** An `azure-pipelines.yml` file automates Playwright test runs in Azure DevOps by defining sequential pipeline tasks: installing Node dependencies (`npm ci`), authenticating via `AzureCLI@2`, running cloud tests, and publishing reports via `PublishPipelineArtifact@1`.

---

## Why This Exists

To enforce continuous quality checks on every code commit to an Azure Repos repository, teams automate test execution using **Azure DevOps Pipelines**. A declarative `azure-pipelines.yml` file checked into the root of your repository defines the exact pipeline steps needed to build, execute, and publish test artifacts automatically.

---

## Mental Model

Imagine an automated airport baggage handling system.
- **Manual Execution:** A baggage handler carrying suitcases one by one from the check-in desk to the airplane.
- **Azure DevOps Pipeline (`azure-pipelines.yml`):** A conveyor belt system programmed with 4 automated gates: Gate 1 checks passenger tickets (`npm ci`), Gate 2 scans security clearance (`AzureCLI@2` login), Gate 3 routes luggage to the plane (`npx playwright test`), and Gate 4 logs luggage manifests (`PublishPipelineArtifact@1`).

---

## Architecture of `azure-pipelines.yml`

```text
┌───────────────────────────────────────┐
│ Task 1: Install Dependencies          │
│ Script: npm ci                        │
└───────────────────────────────────────┘
                    │
                    ▼
┌───────────────────────────────────────┐
│ Task 2: Azure CLI Authentication      │
│ Task: AzureCLI@2                      │
│ Authenticates Azure Subscription      │
└───────────────────────────────────────┘
                    │
                    ▼
┌───────────────────────────────────────┐
│ Task 3: Execute Playwright Suite      │
│ Script: npx playwright test           │
│ --config=playwright.service.config.js │
└───────────────────────────────────────┘
                    │
                    ▼
┌───────────────────────────────────────┐
│ Task 4: Publish HTML Reports          │
│ Task: PublishPipelineArtifact@1       │
└───────────────────────────────────────┘
```

---

## Key Characteristics

- **Declarative Code-as-Pipeline:** `azure-pipelines.yml` is version-controlled alongside application source code.
- **Automated Commit Triggers:** Triggers automated pipeline builds whenever code pushes to `main` or pull requests open.
- **Native Azure Service Connection:** Uses Azure Service Connections to authenticate with Azure Playwright testing endpoints securely without hardcoding API keys.

---

## Common Mistakes

- **Forgetting `npm ci` before test execution:** Attempting to execute `npx playwright test` without running `npm ci` first. The Azure pipeline container lacks installed node modules!
- **Using incorrect variable syntax in Windows vs PowerShell tasks:** Using Unix `$PLAYWRIGHT_SERVICE_URL` syntax inside Windows Command Prompt tasks instead of `$(PLAYWRIGHT_SERVICE_URL)`.

---

## Canonical `azure-pipelines.yml` Example

```yaml
# Azure DevOps CI/CD Pipeline YAML for Playwright Test Suite
trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

variables:
  # Reference Azure Playwright Service URL from pipeline library variable
  PLAYWRIGHT_SERVICE_URL: $(PLAYWRIGHT_SERVICE_URL)

steps:
  # Task 1: Install Node.js runtime environment
  - task: NodeTool@0
    inputs:
      versionSpec: '18.x'
    displayName: 'Install Node.js'

  # Task 2: Install project dependencies
  - script: |
      npm ci
      npx playwright install --with-deps
    displayName: 'Install Dependencies and Browsers'

  # Task 3: Authenticate Azure Service and execute Playwright tests
  - task: AzureCLI@2
    displayName: 'Run Playwright Tests on Azure Cloud'
    inputs:
      azureSubscription: 'MyAzureServiceConnection'
      scriptType: 'bash'
      scriptLocation: 'inlineScript'
      inlineScript: |
        npx playwright test --config=playwright.service.config.js --workers=5

  # Task 4: Publish Playwright HTML report artifact to pipeline build
  - task: PublishPipelineArtifact@1
    condition: always()
    inputs:
      targetPath: 'playwright-report'
      artifact: 'Playwright-HTML-Report'
      publishLocation: 'pipeline'
    displayName: 'Publish Playwright HTML Report'
```

---

## Key Takeaways

- Define CI/CD pipeline steps in `azure-pipelines.yml`.
- Include dependency installation (`npm ci`), Azure CLI authentication (`AzureCLI@2`), and test execution steps.
- Publish HTML report artifacts using `PublishPipelineArtifact@1`.

---

## Related

- [[azure-playwright-cloud-workspace-execution]] — Azure cloud browser execution
- [[jenkins-ci-playwright-integration]] — Jenkins CI integration
- [[node-custom-npm-scripts-automation]] — npm script automation
