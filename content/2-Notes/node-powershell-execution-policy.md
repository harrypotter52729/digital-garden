---
id: 202608151959
title: PowerShell Script Execution Policy and Bypass Configuration
aliases:
  - powershell execution policy
  - script execution disabled error
  - npm ps1 error
tags:
  - type/concept
  - status/processing
  - tool/node
  - topic/environment
date_created: 2026-08-15
mastery_level: 1
---

# PowerShell Script Execution Policy and Bypass Configuration

---

> **TL;DR:** PowerShell Execution Policy is like a safety lock on a lawnmower—by default, Windows locks PowerShell from executing local `.ps1` script files (including `npm.ps1`), requiring developers to explicitly unlock execution permissions for their user account.

---

## Why This Exists

Windows PowerShell includes a built-in security feature called **Execution Policy** designed to prevent users from accidentally running untrusted or malicious automation scripts. Because Node.js package commands on Windows (like `npm` or `npx`) execute through PowerShell script wrappers (`npm.ps1`), a default `Restricted` or `AllSigned` execution policy blocks NPM commands, raising the error:  
`File ...\npm.ps1 cannot be loaded because running scripts is disabled on this system.`

---

## Mental Model

Imagine buying a specialized power tool (`npm`) that comes with a safety switch managed by your workshop supervisor (Windows Security Policy). When you pull the trigger, the supervisor steps in and says, "Unsigned script files are forbidden from executing." To use your power tool for web development, you must show the supervisor that your user account has explicit authorization to bypass script locks on local developer tasks.

---

## How It Works

1. **Policy Inspection:** Check active scope settings via `Get-ExecutionPolicy -List`.
2. **Execution Scope Selection:**
   - `LocalMachine`: Applies system-wide to all user accounts (requires Admin rights).
   - `CurrentUser`: Applies strictly to the active logged-in Windows user (safest approach).
3. **Policy Mode Settings:**
   - `Restricted`: Blocks all `.ps1` script execution (Windows default).
   - `RemoteSigned`: Requires digital signatures for scripts downloaded from the Internet.
   - `Bypass`: Allows all local and imported scripts to run without warning prompts.

---

## Key Characteristics

- **Not an NPM or Node Bug:** Operating system security configuration, not a flaw in Node.js or Playwright.
- **Granular Scoping:** Scoping changes to `CurrentUser` avoids altering system-wide administrative permissions.
- **One-Time Fix:** Resolves script execution permissions across all terminal applications (VS Code terminal, PowerShell, Windows Terminal).

---

## Common Mistakes

- **Reinstalling Node.js or Playwright:** Reinstalling software does not resolve Windows PowerShell Execution Policy restrictions.
- **Changing `LocalMachine` policy unsafely:** Weakening machine-wide security policies when updating `CurrentUser` policy is sufficient.

---

## Best Practices

- Always apply execution policy bypass strictly to `CurrentUser` scope.
- Use an Administrator PowerShell terminal session to execute the policy update once.

---

## Resolution Commands

### Step 1: Open PowerShell as Administrator and Inspect Current Policy

```powershell
# Check execution policies across all scopes
Get-ExecutionPolicy -List
```

### Step 2: Set CurrentUser Policy to Bypass

```powershell
# Grant script execution authorization strictly for the current user
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy Bypass
```

When prompted `Do you want to change the execution policy?`, type `Y` and press `Enter`.

### Step 3: Verify Resolution in Regular Developer Terminal

```powershell
# Test npm execution in VS Code terminal or standard PowerShell prompt
npm init playwright@latest
```

---

## Key Takeaways

- PowerShell blocks `.ps1` script execution by default to protect against unauthorized code execution.
- `npm` relies on `npm.ps1` script wrappers on Windows.
- Running `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy Bypass` permanently resolves NPM execution errors cleanly and safely.

---

## Related

- [[node-runtime-path-setup]] — System PATH configuration
- [[pw-project-initialization]] — Running initial project setup commands
- [[MOC - JS Variables & Execution]]
