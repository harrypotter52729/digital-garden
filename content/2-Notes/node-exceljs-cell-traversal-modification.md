---
id: 202608152053
title: Excel Spreadsheet Cell Traversal and Modification using ExcelJS
aliases:
  - excel cell traversal
  - exceljs cell modification
  - excel workbook read write
tags:
  - type/concept
  - status/processing
  - tool/node
  - lang/js
  - topic/environment
date_created: 2026-08-15
mastery_level: 1
---

# Excel Spreadsheet Cell Traversal and Modification using ExcelJS

---

> **TL;DR:** Reading an `.xlsx` workbook using `workbook.xlsx.readFile()`, nested `eachRow()` and `eachCell()` loops locate cell coordinates dynamically—allowing you to modify `cell.value` and save updates back to disk via `workbook.xlsx.writeFile()`.

---

## Why This Exists

Hardcoding cell coordinates (like cell `C4`) breaks data-driven workflows when spreadsheet rows rearrange or new headers are inserted. To dynamically locate and update spreadsheet values (e.g., searching for "Mango" and updating its price column to `350`), Node.js automation scripts must traverse worksheet rows and columns dynamically, locate target coordinate objects (`{ row, col }`), and write modified values back to disk.

---

## Mental Model

Imagine a librarian updating an index card inside a physical card catalog.
- **Nested Traversal (`eachRow` + `eachCell`):** The librarian pulls out Drawer #1 (`eachRow`), checks card by card (`eachCell`), and reads the book title on each card.
- **Coordinate Modification & Save (`cell.value` + `writeFile`):** When the librarian finds "Mango", they note its location (Row 3, Column 2), erase the old price, write `$350`, and slide the updated drawer back into the cabinet (`writeFile`).

---

## How It Works

1. **Load Workbook and Worksheet:**
   ```javascript
   const workbook = new ExcelJS.Workbook();
   await workbook.xlsx.readFile(filePath);
   const worksheet = workbook.getWorksheet('Sheet1');
   ```
2. **Dynamic Coordinate Search (Nested Loops):**
   ```javascript
   let targetCoord = { row: -1, col: -1 };
   worksheet.eachRow((row, rowNumber) => {
     row.eachCell((cell, colNumber) => {
       if (cell.value === searchText) {
         targetCoord = { row: rowNumber, col: colNumber };
       }
     });
   });
   ```
3. **Offset Cell Modification and Save:**  
   Access target cell (or offset cell for price updates: `targetCoord.col + colOffset`), reassign `cell.value`, and save:
   ```javascript
   const cell = worksheet.getCell(targetCoord.row, targetCoord.col + colOffset);
   cell.value = replacementValue;
   await workbook.xlsx.writeFile(filePath);
   ```

---

## Key Characteristics

- **Dynamic Location Finding:** Locates target text regardless of where it appears in the spreadsheet.
- **Column Offset Traversal:** Move $N$ columns to the right (`col + 2`) to update adjacent data cells in the same row.
- **Asynchronous File I/O:** Requires `await` on `readFile()` and `writeFile()` operations.

---

## Common Mistakes

- **Forgetting `await` on `readFile()` or `writeFile()`:** Omitting `await`, causing script execution to proceed before the Excel file finishes loading or saving to disk.
- **Modifying `cell.value` without calling `writeFile()`:** Updating `cell.value` in memory but forgetting to execute `await workbook.xlsx.writeFile(filePath)`, leaving the physical file on disk unchanged.

---

## Canonical Code Example

```javascript
// Reusable helper function to search and update Excel values dynamically
const ExcelJS = require('exceljs');

async function updateExcelCell(filePath, searchText, replacementValue, colOffset = 0) {
  const workbook = new ExcelJS.Workbook();
  await workbook.xlsx.readFile(filePath);
  const worksheet = workbook.getWorksheet(1); // First worksheet

  let targetCoord = { row: -1, col: -1 };

  // 1. Traverse rows and cells dynamically to locate search text
  worksheet.eachRow((row, rowNumber) => {
    row.eachCell((cell, colNumber) => {
      if (cell.value === searchText) {
        targetCoord = { row: rowNumber, col: colNumber };
      }
    });
  });

  // 2. Modify target cell value (with optional column offset)
  if (targetCoord.row !== -1) {
    const targetCell = worksheet.getCell(targetCoord.row, targetCoord.col + colOffset);
    targetCell.value = replacementValue;

    // 3. Write modified workbook back to physical disk file
    await workbook.xlsx.writeFile(filePath);
    console.log(`Successfully updated "${searchText}" offset cell to: ${replacementValue}`);
  }
}

module.exports = { updateExcelCell };
```

---

## Key Takeaways

- Use nested `eachRow()` and `eachCell()` loops to discover target coordinates dynamically.
- Use `worksheet.getCell(row, col + offset)` to target adjacent data fields in the same row.
- Always save workbook changes to disk via `await workbook.xlsx.writeFile(filePath)`.

---

## Related

- [[node-exceljs-project-setup]] — ExcelJS setup
- [[pw-file-download-upload-spreadsheet-automation]] — Excel upload/download testing
- [[MOC - JS Variables & Execution]]
