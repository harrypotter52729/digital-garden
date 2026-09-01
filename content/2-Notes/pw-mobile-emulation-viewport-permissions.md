---
id: 202608152059
title: Mobile Device Emulation and Geolocation/SSL Permission Overrides
aliases:
  - mobile device emulation
  - devices iPhone 11
  - geolocation permissions
  - ignoreHTTPSErrors
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/environment
date_created: 2026-08-15
mastery_level: 1
---

# Mobile Device Emulation and Geolocation/SSL Permission Overrides

---

> **TL;DR:** Configuring `...devices['iPhone 11']`, `viewport: { width: 375, height: 667 }`, `geolocation`, and `ignoreHTTPSErrors: true` inside `playwright.config.js` emulates mobile touch viewports and overrides browser security prompts automatically.

---

## Why This Exists

Web applications must be tested across desktop viewports, mobile devices, and staging environments with self-signed SSL certificates. Manually dismissing browser location permissions ("Allow site to access your location?") or clicking past untrusted SSL certificate warnings breaks automated test runs. Playwright provides built-in device emulation presets (`devices['iPhone 11']`) and global permission overrides in configuration.

---

## Mental Model

Imagine putting on a virtual reality simulator suit before testing a website.
- **Mobile Device Emulation (`devices['iPhone 11']`):** The suit constrains your vision to an iPhone screen size (375x812 pixels), turns mouse clicks into mobile touch events (`hasTouch: true`), and sends mobile browser User-Agent strings.
- **Permission Overrides (`ignoreHTTPSErrors` & `geolocation`):** The suit automatically presents a security clearance badge to guards asking for location access or SSL certificates, bypassing security prompts without interrupting your walk.

---

## How It Works

### Mobile Device Emulation
Import `devices` from `@playwright/test` and spread target device settings into project configurations:
```javascript
const { devices } = require('@playwright/test');

projects: [
  {
    name: 'Mobile Safari',
    use: {
      ...devices['iPhone 11'],
    },
  },
]
```

### Viewport Customization
Override specific screen dimensions:
`use: { viewport: { width: 1280, height: 720 } }`

### SSL & Permission Overrides
```javascript
use: {
  ignoreHTTPSErrors: true, // Bypasses self-signed SSL certificate warnings
  permissions: ['geolocation'], // Grants location permissions automatically
  geolocation: { latitude: 37.7749, longitude: -122.4194 }, // Sets mock GPS location
}
```

---

## Key Characteristics

- **Pre-Built Device Library:** Includes presets for iPhones, iPads, Android Pixels, and Galaxy devices (`devices['Pixel 5']`).
- **Touch & User-Agent Emulation:** Configures touch screen events (`hasTouch: true`), device scale factors, and mobile User-Agent headers.
- **Automated SSL Clearance:** Eliminates `NET::ERR_CERT_AUTHORITY_INVALID` test failures on internal staging servers.

---

## Common Mistakes

- **Testing desktop-only UI flows on mobile device emulators:** Running tests that depend on hover menus or desktop sidebars on mobile device viewports. Mobile viewports trigger responsive hamburger menus, causing desktop locators to fail.
- **Forgetting spread syntax `...devices['Device Name']`:** Writing `use: devices['iPhone 11']` instead of spreading `use: { ...devices['iPhone 11'] }`.

---

## Canonical Config Example

```javascript
// playwright.config.js device emulation and permission configuration
const { defineConfig, devices } = require('@playwright/test');

module.exports = defineConfig({
  use: {
    // 1. Bypass self-signed SSL certificate warnings on staging
    ignoreHTTPSErrors: true,

    // 2. Automatically grant location permissions & mock GPS coordinates
    permissions: ['geolocation'],
    geolocation: { latitude: 40.7128, longitude: -74.0060 }, // New York City
  },

  projects: [
    {
      name: 'Desktop Chrome',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'Mobile Safari (iPhone 11)',
      use: {
        ...devices['iPhone 11'],
      },
    },
  ],
});
```

---

## Key Takeaways

- Emulate mobile devices using `use: { ...devices['iPhone 11'] }` in project configs.
- Bypass SSL certificate warnings using `ignoreHTTPSErrors: true`.
- Automatically grant location access using `permissions: ['geolocation']` and `geolocation: { lat, long }`.

---

## Related

- [[pw-cli-execution-flags]] — Running specific projects via CLI
- [[pw-configuration-file]] — Playwright configuration architecture
- [[MOC - Playwright Architecture]]
