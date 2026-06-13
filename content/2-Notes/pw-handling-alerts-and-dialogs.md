---
id: 202606131229
title: Handling Alerts & Dialogs
aliases:
  - playwright-dialogs
  - playwright-javascript-alerts
tags:
  - type/concept
  - tool/playwright
  - status/processing
  - topic/locating-interacting
date_created: 2026-06-13
mastery_level: 1
---

# Handling Alerts & Dialogs

> **TL;DR:** JavaScript dialog boxes (`window.alert`, `window.confirm`, `window.prompt`) are synchronous modal dialogs that halt the browser's execution thread. Playwright **automatically dismisses** these dialogs by default so your test execution never freezes. To accept or type into a dialog, you must register a listener callback *before* the action that triggers the dialog occurs.

## The Automated Receptionist Analogy

Imagine a door-to-door solicitor knocking on your office door. In traditional automation, the entire office shuts down, waiting for a human to answer. 

Playwright installs an **automated receptionist** at the front desk:

```
[ Click Button ]  ------> Triggers javascript alert/confirm window
                               |
                               v
                       [ Dialog Event ]
                               |
         +---------------------+---------------------+
         | (Default Behavior)                        | (Custom Listener)
         v                                           v
[ Auto-Dismiss / Reject ]                  [ Read message, fill text, ]
- Prevents test suite freeze               [ click Accept programmatically ]
```

- **Default Behavior:** The receptionist politely slams the door: *"No thank you!"* (auto-dismisses the alert/confirm popup immediately) so the office (the browser thread) can keep working.
- **Custom Listener (Pre-programed instruction):** You tell the receptionist *in advance*: *"If a delivery driver knocks, ask for their name, sign the receipt, and let them in."* (`page.on('dialog', handler)`).

---

## Dialog Types in Playwright

Playwright wraps browser-native dialogs into a single `Dialog` class, supporting:
- **`alert`**: Simple message with an "OK" button.
- **`confirm`**: Question with "OK" (accept) and "Cancel" (dismiss) options.
- **`prompt`**: Dialog requesting text input from the user.
- **`beforeunload`**: Warning displayed when a user attempts to navigate away from an unsaved page.

---

## Canonical Code Example

This code demonstrates how to register dialog listeners to verify messages, accept confirmations, and fill in text prompts:

```javascript
// @ts-check
const { test, expect } = require("@playwright/test");

test("Interacting with JS Dialogs (Alerts, Confirms, Prompts)", async ({ page }) => {
  await page.goto("/javascript_alerts");

  // --- 1. Handling an Alert (OK only) ---
  
  // Register the listener BEFORE clicking the trigger button
  page.once("dialog", async (dialog) => {
    console.log(`Alert message: ${dialog.message()}`);
    expect(dialog.type()).toBe("alert");
    await dialog.accept(); // Clicks OK
  });

  // Clicking triggers the alert. The once() listener fires and resolves it.
  await page.getByRole("button", { name: "Click for JS Alert" }).click();
  await expect(page.locator("#result")).toHaveText("You successfully clicked an alert");

  // --- 2. Handling a Confirm Dialog (OK / Cancel) ---

  // Register a listener that chooses to cancel (dismiss) the modal
  page.once("dialog", async (dialog) => {
    expect(dialog.type()).toBe("confirm");
    expect(dialog.message()).toContain("Are you sure");
    await dialog.dismiss(); // Clicks Cancel
  });

  await page.getByRole("button", { name: "Click for JS Confirm" }).click();
  await expect(page.locator("#result")).toHaveText("You clicked: Cancel");

  // --- 3. Handling a Prompt Dialog (Text Input) ---

  // Register a listener that fills input and accepts
  page.once("dialog", async (dialog) => {
    expect(dialog.type()).toBe("prompt");
    // Enter text into the prompt dialog before accepting it
    await dialog.accept("Senior SDET"); 
  });

  await page.getByRole("button", { name: "Click for JS Prompt" }).click();
  await expect(page.locator("#result")).toHaveText("You entered: Senior SDET");
});
```

---

## Related
* [[js-event-listeners-and-event-objects]] - The event emitter pattern underlying Playwright's `page.on` callback handlers.
* [[pw-actionability-and-auto-waiting]] - How blocking UI overlays interact with Playwright's action thread.
* [[js-event-listeners-and-event-objects]] - Listens for native dialog events using standard callback listener patterns.
