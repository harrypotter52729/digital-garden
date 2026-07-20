---
id: 202607201916
title: "String Searching: indexOf"
aliases:
  - string-indexof
  - indexof
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
date_created: 2026-07-20
mastery_level: 1
---

# String Searching: indexOf

> **TL;DR:** `indexOf()` is like asking a librarian **which shelf number** a book starts on (returns the exact **starting index** of the first match, or `-1` if it is not in the library).

## Purpose & Behavior
The `indexOf()` method returns the index of the first occurrence of a specified substring. If the substring is not found, it returns `-1` (a sentinel value indicating no match).

```javascript
string.indexOf(searchString, startIndex)
```

* **`searchString`**: The substring you want to locate.
* **`startIndex` (Optional)**: The index at which to begin the search. Defaults to `0`.

---

## Technical Details & Mechanics

### 1. The sentinel value (`-1`)
Since `0` is a valid starting index in JavaScript, the designers chose `-1` to represent "not found". This is a sentinel value indicating a special condition rather than a valid index.

### 2. The Classic Interview Trap (Truthy/Falsy Bug)
A very common developer bug is checking for the existence of a substring by writing:

```javascript
if (str.indexOf("admin")) { ... } // Classic bug!
```

* **Why it fails:** If the string is `"adminDashboard"`, `str.indexOf("admin")` returns `0`. 
* In JavaScript, `0` is coerced to `false`. Therefore, the block will **not** execute, even though the substring exists!
* **Correct Usage:**
  ```javascript
  if (str.indexOf("admin") !== -1) { ... }
  ```
  *(Or better, if you only care about existence, use [[js-string-includes|includes()]] instead).*

### 3. Positional Searching (`startIndex`)
The second parameter specifies **where the search begins**, not where the match must start.
* If `startIndex` is provided, the engine ignores matches that occur before that index.
* It only moves **forward** through the string.

---

## Edge Cases & Quirks

* **Empty String Lookup:** 
  * `"hello".indexOf("")` returns `0`.
  * `"hello".indexOf("", 3)` returns `3`.
  * The empty string is considered to match at any valid index position up to the length of the string.
* **Search String Longer Than Source:** `"hi".indexOf("hello")` returns `-1`.
* **Type Coercion:** Non-string arguments are coerced to strings.
  * `"12345".indexOf(23)` becomes `"12345".indexOf("23")`, returning `1`.
  * `"abc".indexOf()` implicitly searches for `"undefined"`, returning `-1`.

---

## Time Complexity
* **Worst Case:** $O(n \times m)$ where $n$ is string length and $m$ is search string length.
* **Engine Optimizations:** JavaScript engines use highly optimized substring search algorithms under the hood, making matches much faster than a naive character-by-character search.

---

## Canonical Code Example

Here is a practical script demonstrating domain extraction, index validation, and common interview traps:

```javascript
// --- 1. Real World: Extracting Domain ---
const email = "luffy@gmail.com";
const atIndex = email.indexOf("@");

if (atIndex !== -1) {
  const domain = email.slice(atIndex + 1);
  console.log("Extracted Domain:", domain); // "gmail.com"
}


// --- 2. Demonstrating the Truthy/Falsy Bug ---
const adminRoute = "/admin/settings";

// BAD: evaluates to 0 (falsy)
if (adminRoute.indexOf("/admin")) {
  console.log("This will NOT print, even though it matches!");
}

// GOOD: explicit check
if (adminRoute.indexOf("/admin") !== -1) {
  console.log("Correctly verified presence at index 0"); // Prints
}


// --- 3. Positional search (Finding occurrences) ---
const text = "banana";
const firstAna = text.indexOf("ana"); // Starts at index 1
const secondAna = text.indexOf("ana", 2); // Starts at index 3

console.log("First occurrence of 'ana':", firstAna);   // 1
console.log("Second occurrence of 'ana':", secondAna); // 3 (ignores index 1)


// --- 4. Coercion Quirks ---
console.log("Coerced number search:", "12345".indexOf(34)); // 2
console.log("Omitted argument search:", "abc".indexOf());   // -1 (searches for "undefined")
console.log("Empty string match:", "hello".indexOf("", 4));  // 4
```

---

## Related
* [[js-string-includes]] - Checking for substring existence returning a boolean.
* [[js-string-lastindexof]] - Finding the last occurrence index by searching backward.
* [[js-string-character-access]] - Traditional index lookup details.
* [[js-string-slice]] - Extracting a section of a string using indexes.
* [[MOC - JS Built-in Objects & Utilities]] - Central MOC index for standard utility methods.
