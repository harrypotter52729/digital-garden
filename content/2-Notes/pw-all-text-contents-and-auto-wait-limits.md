---
id: 202608152012
title: Array Text Extraction with allTextContents() and Auto-Wait Limitations
aliases:
  - allTextContents
  - auto-wait limits
  - extracting text arrays
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Array Text Extraction with allTextContents() and Auto-Wait Limitations

---

> **TL;DR:** `allTextContents()` takes a rapid snapshot of all text strings matching a selector, but unlike single-element methods, it does NOT auto-wait for dynamic list items to finish loading—returning an empty array `[]` if called before the page finishes rendering.

---

## Why This Exists

Extracting text arrays across list items (e.g., scraping 10 product titles or dropdown menu options) requires `locator.allTextContents()`. However, because lists can legitimately contain zero items on a web page, Playwright cannot distinguish between an empty list and a list that is still loading asynchronously. As a result, `allTextContents()` bypasses automatic waiting, making explicit synchronization necessary before calling it.

---

## Mental Model

Imagine walking into a warehouse and taking a photo of a shelf array.
- **Single-Item Method (`textContent()`):** "Wait at shelf #1 until a box is placed on it, then read the label." (Auto-waits).
- **Array-Item Method (`allTextContents()`):** "Snap a photo of all boxes on the shelf RIGHT NOW." If the delivery truck is still driving up the driveway, the photo captures an empty shelf (`[]`), and the photographer walks away assuming no boxes exist.

---

## How It Works

1. **`allTextContents()` Execution:** Immediately scans the current DOM state for all elements matching the locator, returning an array of strings (`Promise<string[]>`).
2. **Auto-Wait Limitation:** Does **NOT** wait for element visibility or attachment! If executed while API data is fetching in the background, it instantly returns `[]`.
3. **Synchronization Pattern:** To prevent empty array results, developers must pair `allTextContents()` with an explicit pre-wait assertion (such as `await expect(locator.first()).toBeVisible()` or `await locator.first().waitFor()`).

---

## Key Characteristics

- **Returns String Array:** Collects text strings from all matching elements into a native JavaScript array (`['iPhone X', 'Samsung Note 8', ...]`).
- **No Built-in Auto-Wait:** Operates instantly on current DOM state without waiting for asynchronous network responses.
- **Requires Pre-Wait Synchronization:** Must be preceded by a single-element wait step to guarantee array populated status.

---

## Common Mistakes

- **Calling `allTextContents()` right after a button click or navigation:** Triggering `const titles = await page.locator('.title').allTextContents()` immediately after clicking submit. Because the page hasn't finished rendering list items, `titles` evaluates to `[]`.
- **Assuming `allTextContents()` retries automatically:** Expecting `allTextContents()` to retry until items appear (only web-first assertions like `expect(locator).toHaveText([...])` retry automatically).

---

## Code Comparison

### Incorrect (Flaky — Returns Empty Array `[]`)

```javascript
// BROKEN: Executes instantly before asynchronous list items finish loading!
await page.click('#login-button');
const titles = await page.locator('.product-title').allTextContents(); 
console.log(titles); // Output: [] (Empty array because rendering wasn't complete!)
```

### Correct (Reliable — Synchronized with Single-Element Wait)

```javascript
// RELIABLE: Waits for the first item to attach/render before taking snapshot
await page.click('#login-button');

const productTitles = page.locator('.product-title');

// 1. Explicitly wait for the FIRST element to show up in the DOM
await productTitles.first().waitFor(); 

// 2. Now safely extract text array across all populated elements
const titles = await productTitles.allTextContents(); 
console.log(titles); // Output: ['iPhone X', 'Samsung Note 8', 'Nokia Edge', 'Blackberry']
```

---

## Key Takeaways

- `allTextContents()` extracts an array of text strings across all matching selector elements.
- Bypasses automatic waiting—if called before list elements render, it returns an empty array `[]`.
- Always precede `allTextContents()` with `await locator.first().waitFor()` or a web-first assertion.

---

## Related

- [[pw-multiple-element-locators]] — Targeting individual list elements
- [[pw-dynamic-load-state-waiting]] — Dynamic waiting techniques
- [[MOC - Playwright Locators and Actions]]
