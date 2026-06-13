---
id: 202606131218
title: JS & Automation Cheatsheet
aliases:
  - JavaScript Cheatsheet
  - JS Automation Cheatsheet
tags:
  - type/reference
  - lang/js
  - status/processing
  - topic/cheatsheet
date_created: 2026-06-13
date_modified: 2026-06-13
mastery_level: 1
---

# JS & Automation Cheatsheet

> **TL;DR:** Quick syntax reference for core JavaScript ES6+ features and Playwright E2E automation actions. 

---

## 1. JavaScript Core Fundamentals

### Variables & Scopes
- **`const`**: Blocks reassignment of the variable binding. Use as default.
- **`let`**: Block-scoped variable that allows reassignment.

### Arrow Functions
```javascript
// Single-line implicit return
const add = (a, b) => a + b;

// Multi-line explicit return
const getSearchURL = (query) => {
  const sanitized = query.trim().toLowerCase();
  return `https://site.com/search?q=${sanitized}`;
};
```

### Array Methods
```javascript
const items = [{ name: "iPhone", price: 999 }, { name: "Case", price: 29 }];

// .map() - Transform elements
const names = items.map(item => item.name); // ["iPhone", "Case"]

// .filter() - Filter elements matching condition
const expensive = items.filter(item => item.price > 100); // [{ name: "iPhone", price: 999 }]

// .find() - Retrieve first match
const iphone = items.find(item => item.name === "iPhone");
```

### Promises & Async/Await
```javascript
// Every browser action returns a Promise that must be awaited
async function fetchTitle(page) {
  try {
    await page.goto("https://site.com");
    const title = await page.title();
    return title;
  } catch (error) {
    console.error("Navigation failed:", error);
  }
}
```

### Safe Navigation & Fallbacks
```javascript
// Optional Chaining (?.)
const street = user?.address?.street; // returns undefined if address is null/undefined

// Nullish Coalescing (??)
const role = user?.role ?? "Guest"; // defaults to "Guest" if role is null or undefined
```

---

## 2. Playwright Automation API

### Locators
Select elements reliably using user-facing roles.
```javascript
page.getByRole("button", { name: "Submit" }); // Find by role and text name
page.getByPlaceholder("Enter Email");        // Find by input placeholder
page.getByText("ACCOUNT CREATED!");           // Find by visible text
page.locator(".card-product");                // Fallback to CSS/XPath selectors
```

### Basic Actions
```javascript
await page.goto("/login");                    // Navigate
await page.fill("#username", "Hemanth");      // Fill input fields
await page.click("button.submit");            // Click element
await page.selectOption("#country", "India"); // Select dropdown option
await page.check('input[type="checkbox"]');   // Check checkbox
await page.setInputFiles("input#file", path);  // Upload file
```

### Web Assertions (Auto-Retrying)
```javascript
await expect(page).toHaveTitle("Home Page");
await expect(page).toHaveURL(/view_cart/);
await expect(page.getByText("Logged in")).toBeVisible();
await expect(page.locator(".item")).toHaveCount(3);
```

---

## Connections
This cheatsheet connects directly to core concepts documented in your vault:
* [[js-var-let-const]] - Variable scoping rules.
* [[js-arrow-functions-and-lexical-this]] - Arrow syntax bindings.
* [[js-closures-and-lexical-environment]] - Scopes retained by nested functions.
* [[js-primitive-vs-reference-types]] - Heap vs Stack value storage details.
* [[js-the-event-loop-and-call-stack]] - Promise execution cycles.
* [[js-json-parse-and-stringify]] - Serializing test objects to JSON text.
* [[js-optional-chaining-operator]] - Safe property search paths.
* [[js-dom-tree-and-element-selection]] - Selecting HTML tree elements.

---

## References
* [Playwright Official Documentation](https://playwright.dev/docs/intro)
* [MDN Web Docs - JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
