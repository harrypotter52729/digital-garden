---
id: 202608152036
title: Calendar Component Validation via Array-to-Array Comparisons
aliases:
  - calendar assertion
  - inputValue validation
  - array-to-array assertion
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/assertions
date_created: 2026-08-15
mastery_level: 1
---

# Calendar Component Validation via Array-to-Array Comparisons

---

> **TL;DR:** Asserting selected calendar dates by grouping expected `[month, day, year]` values into an array, iterating over component input fields, and verifying `inputValue()` against array indices ensures complete data integrity without fragile string parsing.

---

## Why This Exists

Web applications often render selected dates across multiple adjacent `<input>` fields or separate dropdown components (`[Month] [Day] [Year]`). Verifying each input field individually requires verbose repetitive code. Constructing expected value arrays (`expectedList = [month, day, year]`) and comparing them against DOM input fields inside a `for` loop validates calendar state cleanly.

---

## Mental Model

Imagine inspecting a three-number combination lock (`06 - 15 - 2027`).
Instead of writing three separate inspection reports for Tumbler 1, Tumbler 2, and Tumbler 3, you create an expected combination array `[6, 15, 2027]`. You spin each tumbler in order (`for` loop), reading its current displayed digit (`inputValue()`), and check off each digit against your array list.

---

## How It Works

1. **Expected Array Construction:** Store target date parameters inside an array:
   `const expectedList = [monthNumber, targetDate, targetYear];`
2. **Common Input Collection:** Query the common parent input container:
   `const inputs = page.locator('.date-input-group input');`
3. **Loop Iteration & `inputValue()` Extraction:** Loop through inputs and extract actual values using `inputValue()`:
   ```javascript
   for (let i = 0; i < expectedList.length; ++i) {
     const actualValue = await inputs.nth(i).inputValue();
     expect(actualValue).toEqual(expectedList[i]);
   }
   ```

---

## Key Characteristics

- **Concise Multi-Input Assertions:** Validates multiple input elements in a single compact loop.
- **Dynamic Attribute Extraction:** Uses `inputValue()` (or `getAttribute('value')`) to extract dynamically set input values.
- **Scalable Verification:** Expanding assertions to include hours, minutes, or seconds requires simply pushing items to `expectedList`.

---

## Common Mistakes

- **Using `textContent()` on form input elements:** `textContent()` returns empty string `""` for `<input>` elements. Always use `inputValue()`.
- **Mismatching array element types:** Comparing numeric string `'6'` against integer `6` using strict equality. Standardize array items as strings or numbers before assertion.

---

## Canonical Code Example

```javascript
// Array-to-array iteration validating calendar date component inputs
const { test, expect } = require('@playwright/test');

test('validate selected date values across input group', async ({ page }) => {
  await page.goto('https://example.com/calendar-demo');

  const monthNumber = '6';
  const targetDate = '15';
  const targetYear = '2027';

  // 1. Create expected values array [Month, Day, Year]
  const expectedList = [monthNumber, targetDate, targetYear];

  // 2. Query common parent container input elements
  const dateInputs = page.locator('.input-group-container input');

  // 3. Iterate over input elements and assert extracted inputValue() against expected array
  for (let i = 0; i < expectedList.length; ++i) {
    const actualInputValue = await dateInputs.nth(i).inputValue();
    console.log(`Input Index ${i}: Actual="${actualInputValue}" | Expected="${expectedList[i]}"`);
    expect(actualInputValue).toEqual(expectedList[i]);
  }
});
```

---

## Key Takeaways

- Group expected date components into an `expectedList` array.
- Extract live form values using `inputValue()`.
- Iterate inputs inside a `for` loop to compare DOM values against array indices.

---

## Related

- [[pw-calendar-date-picker-automation]] — Date-picker automation strategy
- [[pw-text-content-vs-input-value]] — `textContent()` vs `inputValue()`
- [[MOC - Playwright Assertions]]
