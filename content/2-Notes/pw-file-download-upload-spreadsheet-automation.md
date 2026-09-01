---
id: 202608152054
title: Automated File Download, In-Memory Excel Modification, and Upload
aliases:
  - file upload download automation
  - waitForEvent download
  - setInputFiles
  - excel upload testing
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/execution
date_created: 2026-08-15
mastery_level: 1
---

# Automated File Download, In-Memory Excel Modification, and Upload

---

> **TL;DR:** `page.waitForEvent('download')` captures file downloads, ExcelJS updates the spreadsheet data programmatically on disk, and `locator.setInputFiles('path')` uploads the updated file back to the UI—completing full end-to-end data processing validation.

---

## Why This Exists

Enterprise workflows frequently involve downloading an Excel data sheet, updating values (like bulk product prices), and re-uploading the file to a web portal. Playwright provides `page.waitForEvent('download')` to capture file downloads safely without OS popup interference, and `locator.setInputFiles()` to upload files back to `<input type="file">` elements automatically.

---

## Mental Model

Imagine an automated document processing robot.
1. **Download (`waitForEvent('download')`):** The robot holds out a basket (`downloadPromise`) before pushing the "Export Data" button. As the file drops out of the chute, the robot catches it cleanly.
2. **In-Memory Modification (`ExcelJS`):** The robot opens the spreadsheet, updates row #4 price from $299 to $350, and saves the file.
3. **Upload (`setInputFiles()`):** The robot inserts the modified spreadsheet directly into the scanner slot (`setInputFiles()`) without needing human OS file dialog interaction.

---

## How It Works

```text
┌───────────────────────────────────────┐
│ 1. Capture Download Event             │
│    waitForEvent('download')           │
└───────────────────────────────────────┘
                    │
                    ▼
┌───────────────────────────────────────┐
│ 2. Save Downloaded File to Disk       │
│    await download.saveAs(filePath)    │
└───────────────────────────────────────┘
                    │
                    ▼
┌───────────────────────────────────────┐
│ 3. Update Excel File via ExcelJS      │
│    await updateExcelCell(filePath...) │
└───────────────────────────────────────┘
                    │
                    ▼
┌───────────────────────────────────────┐
│ 4. Upload Modified File Back to UI    │
│    setInputFiles(filePath)            │
└───────────────────────────────────────┘
```

---

## Key Characteristics

- **OS Dialog Bypass:** Operates silently without needing desktop GUI OS file pickers.
- **Race Condition Prevention:** Registering `waitForEvent('download')` before clicking download buttons guarantees files complete downloading before reading.
- **FileInput Tag Requirement:** `setInputFiles()` requires the target input element to have `type="file"`.

---

## Common Mistakes

- **Awaiting `click()` BEFORE `waitForEvent('download')`:** Calling `page.click('#download-btn')` *before* `waitForEvent('download')`. The download finishes before the listener attaches, causing the script to hang!
- **Targeting elements without `type="file"`:** Calling `setInputFiles()` on a custom `<div>` wrapper instead of the underlying `<input type="file">` tag.

---

## Canonical Code Example

```javascript
// Full end-to-end file download, Excel data modification, and upload test spec
const { test, expect } = require('@playwright/test');
const { updateExcelCell } = require('./utils/excelUtil');
const path = require('path');

test('download excel file, update price, re-upload, and verify UI table', async ({ page }) => {
  await page.goto('https://example.com/upload-download-demo');

  const filePath = path.join(__dirname, 'downloaded_data.xlsx');

  // 1. Setup download event listener concurrently with click action
  const [download] = await Promise.all([
    page.waitForEvent('download'),
    page.getByRole('button', { name: 'Download' }).click(),
  ]);

  // Save downloaded file to disk
  await download.saveAs(filePath);

  // 2. Modify Excel data in-memory (Update "Mango" price to 350)
  await updateExcelCell(filePath, 'Mango', 350, 2); // colOffset = 2

  // 3. Upload modified Excel file back to web application
  const fileInput = page.locator('input[type="file"]');
  await fileInput.setInputFiles(filePath);

  // 4. Verify updated price renders in UI table
  const mangoRow = page.getByRole('row').filter({ hasText: 'Mango' });
  await expect(mangoRow.locator('.price-cell')).toHaveText('350');
});
```

---

## Key Takeaways

- Wrap `page.waitForEvent('download')` and `downloadBtn.click()` inside `Promise.all()` to catch downloads safely.
- Modify downloaded spreadsheet files using `ExcelJS`.
- Upload updated files using `locator.setInputFiles(filePath)`.

---

## Related

- [[node-exceljs-cell-traversal-modification]] — Excel cell modification
- [[pw-scoped-row-filtering-assertions]] — Table row filtering
- [[MOC - Playwright Locators and Actions]]
