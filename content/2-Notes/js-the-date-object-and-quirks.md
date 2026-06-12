---
id: 202606120835
title: The Date Object & Quirks
aliases:
  - date-object
  - js-dates
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/objects
date_created: 2026-06-12
mastery_level: 1
---

# The Date Object & Quirks

> **TL;DR:** The Date Object is like an **unreliable old pocket watch** that ticks relative to London time in 1970 (Unix Epoch) and has months that start counting from 0 (January is 0) but days that start from 1.

## The Mental Model
Under the hood, JavaScript doesn't store dates as "October 14th, 2026." It stores dates as a **single, massive number**: the number of milliseconds that have passed since midnight of January 1, 1970 UTC (known as the **Unix Epoch**).

Any date object you create is just a wrapper around this epoch timestamp number, presenting it in a human-readable calendar format.

---

## The Core Quirks of JS Dates

### 1. Zero-Indexed Months
In JavaScript, months are zero-indexed, meaning the count starts at `0` instead of `1`:
- `0` = January
- `1` = February
- ...
- `11` = December

However, the days of the month (`getDate()`) start at `1`. This causes constant bugs:
```javascript
// You think this is October 15th
const date = new Date(2026, 10, 15); 
// It is actually NOVEMBER 15th! (10 = November)
```

### 2. Day-of-Week Zero-Index
- `getDay()` returns the day of the week, which is also 0-indexed: `0` is Sunday, `6` is Saturday.

### 3. Date String Parsing Inconsistency
Depending on the exact string formatting you write, the browser will interpret it either as **UTC (London time)** or **Local Time**:
- **`2026-06-12`** (ISO format with dashes): Parsed as **UTC**. If you are in New York (GMT-5), this might display as June 11th evening!
- **`2026/06/12`** (Dashes replaced by slashes): Parsed as **Local Time**.

### 4. Dates are Mutable
Unlike strings or numbers, Date objects are [[js-primitive-vs-reference-types|reference types]] and are **mutable in-place**. Modifying a date instance changes its value directly, which can cause side effects if other code is sharing that date reference.

---

## Canonical Code Example

Here is a script demonstrating month indexing, string parsing differences, and safe modification:

```javascript
// --- 1. Month Zero-Indexing Quirk ---
// Creating: Year: 2026, Month: June (index 5), Day: 12
const eventDate = new Date(2026, 5, 12); 
console.log("Month index:", eventDate.getMonth()); // 5 (June)
console.log("Day of month:", eventDate.getDate()); // 12


// --- 2. String Parsing Quirks (UTC vs Local) ---
const isoDash = new Date("2026-06-12"); // Interpreted as UTC (GMT)
const slashLocal = new Date("2026/06/12"); // Interpreted as Local Time

console.log("Dash timezone value:", isoDash.toISOString());
console.log("Slash timezone value:", slashLocal.toISOString());


// --- 3. Mutation Quirk & Safe Cloning ---
const originalDate = new Date(2026, 0, 1); // Jan 1, 2026
const sharedReference = originalDate;

// Modifying the shared date
sharedReference.setDate(15); 
console.log(originalDate.getDate()); // 15! The original date was mutated.

// Solution: Safely clone the date using its millisecond timestamp
const pristineDate = new Date(2026, 0, 1);
const safeClone = new Date(pristineDate.getTime()); // Cloned by value
safeClone.setDate(15);

console.log(pristineDate.getDate()); // 1 (Safely unchanged!)
console.log(safeClone.getDate());    // 15
```

---

## Related
* [[MOC - JS Data Types & Memory]] - Dates residing in heap memory.
* [[js-primitive-vs-reference-types]] - Dates behave as reference types.
* [[js-json-parse-and-stringify]] - Hydrating dates from JSON text.
