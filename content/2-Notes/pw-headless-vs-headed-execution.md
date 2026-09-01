---
id: 202608152007
title: Headless vs Headed Execution Modes in Playwright
aliases:
  - headless mode
  - headed mode
  - browser visibility
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/execution
date_created: 2026-08-15
mastery_level: 1
---

# Headless vs Headed Execution Modes in Playwright

---

> **TL;DR:** Headless mode is like running a ghost car on an invisible test track at maximum speed, while headed mode turns on the lights and rolls down the windows so you can visually watch the driver steer.

---

## Why This Exists

Browser automation requires balancing execution speed with visual debuggability. In CI/CD pipelines, servers lack graphics rendering monitors, requiring fast, displayless (headless) browser execution. During local test development, engineers require visual feedback (headed mode) to observe layout rendering and user interaction flows.

---

## Mental Model

Think of a radio broadcast versus a live television show.
- **Headless Mode (Radio):** Execution events happen purely in background memory without rendering pixels to a physical monitor screen. Fast, lightweight, and perfect for automated CI servers.
- **Headed Mode (Live TV):** Renders the browser UI window on your desktop monitor, allowing you to watch buttons being clicked and input fields filled in real time.

---

## How It Works

1. **Default Engine State:** Playwright defaults to `headless: true` for high execution speed and CI server compatibility.
2. **Config Level Control:** Set `headless: false` inside the `use` block of `playwright.config.js`.
3. **CLI Level Control:** Pass `--headed` on command line execution (`npx playwright test --headed`) to override config options dynamically without altering code files.

---

## Key Characteristics

- **Zero Behavior Variance:** Playwright's patched browser engines render identical DOM structures and execute identical JavaScript in both headless and headed modes.
- **CI/CD Compatibility:** Headless mode allows tests to execute seamlessly inside headless Linux Docker containers.
- **Performance:** Headless execution avoids operating system window rendering overhead, resulting in 20–30% faster execution.

---

## Common Mistakes

- **Hardcoding `headless: false` in `playwright.config.js`:** Committing `headless: false` causes automated build pipelines (Jenkins, GitHub Actions, Azure DevOps) to fail due to missing display servers (`X11` or `xvfb`).
- **Assuming headless mode behaves differently than headed mode:** Modern browser engines (Chromium, Firefox, WebKit) use identical rendering trees in both modes.

---

## Best Practices

- Keep `headless: true` as the default in `playwright.config.js`.
- Use the CLI flag `--headed` during local feature development and debugging.

---

## Code and Command Matrix

### Configuration (`playwright.config.js`)

```javascript
module.exports = {
  use: {
    // Set global default mode (Recommended: true for CI safety)
    headless: true,
  },
};
```

### CLI Command Execution

```bash
# Execute tests in headless mode (default)
npx playwright test

# Force execution in visible browser window (headed mode)
npx playwright test --headed

# Combine headed mode with slow-motion delay (100ms between actions) for visual inspection
npx playwright test --headed --project=chromium
```

---

## Key Takeaways

- Headless mode executes in memory without UI rendering for high speed in CI.
- Headed mode renders visible browser windows on your desktop for visual debugging.
- Use `npx playwright test --headed` to toggle headed execution without editing config files.

---

## Related

- [[pw-configuration-file]] — Managing global context settings
- [[pw-cli-execution-flags]] — Command-line flags guide
- [[MOC - Playwright Execution]]
