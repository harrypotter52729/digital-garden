---
id: 202607201915
title: "String Searching: includes"
aliases:
  - string-includes
  - includes
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
date_created: 2026-07-20
mastery_level: 1
---

# String Searching: includes

> **TL;DR:** `includes()` is like checking if a **specific book is on a shelf** (you don't care where it is, how thick it is, or what pages it has; you just want a quick **yes or no**).

## Purpose & Behavior
The `includes()` method performs a case-sensitive, literal search to determine whether one string may be found within another string, returning `true` or `false` as appropriate.

```javascript
string.includes(searchString, startIndex)
```

* **`searchString`**: The substring you are searching for.
* **`startIndex` (Optional)**: The position within the string at which to begin searching. Defaults to `0`.

---

## Technical Details & Mechanics

### 1. Case Sensitivity
String comparisons in `includes()` are case-sensitive. It compares Unicode character code values directly (e.g., `J` $\neq$ `j`).
To perform a case-insensitive search, you must normalize both strings:
```javascript
str.toLowerCase().includes(searchStr.toLowerCase())
```
*(Note: Because strings are [[js-string-immutability|immutable]], case conversion methods like `toLowerCase()` return a brand new string rather than modifying the original.)*

### 2. Positional Searching (`startIndex`)
The second parameter specifies **where the search begins**, not where the match must start. 
* If `startIndex` is specified, the engine ignores all characters before that index.
* It does not look backward.
* If the `startIndex` is greater than or equal to the string's length, the search starts beyond the end of the string and returns `false` (unless searching for an empty string).

---

## Edge Cases & Quirks

* **Empty String Search:** `"hello".includes("")` returns `true`. The empty string is considered to exist at every boundary position (e.g. `|h|e|l|l|o|`).
* **Search String Longer Than Source:** `"hi".includes("hello")` returns `false` because a longer substring cannot fit inside a shorter one.
* **Type Coercion:** Non-string arguments are coerced to strings.
  * `"12345".includes(23)` becomes `"12345".includes("23")`, returning `true`.
  * `"hello".includes()` implicitly searches for `"undefined"`, returning `false`.
* **No Regex Support:** It performs a literal substring search. Searching for a pattern like `"hello".includes("h.*o")` will search for the literal string `"h.*o"`, returning `false`.

---

## Time Complexity
* **Worst Case:** $O(n \times m)$ where $n$ is the source string length and $m$ is the search string length.
* **Practical Performance:** Modern engines (like V8) optimize substring matching internally, so real-world execution is generally highly optimized.

---

## Canonical Code Example

Here is a copy-pasteable script demonstrating validation, search filters, and case normalization:

```javascript
// --- 1. Basic Validation ---
function checkEmailDomain(email) {
  // Simple check for presence of '@'
  return email.includes("@");
}

console.log("Email '@' check:", checkEmailDomain("luffy@gmail.com")); // true


// --- 2. Positional Search ---
const filename = "backup.tar.gz";

// Start search at index 7 (ignores "backup.")
console.log("Has 'tar' from index 7:", filename.includes("tar", 7));   // true
console.log("Has 'backup' from index 7:", filename.includes("backup", 7)); // false (ignored prefix)


// --- 3. Case Insensitive Filtering ---
const product = "Apple iPhone 16 Pro";
const searchQuery = "iphone";

// Normalize both strings to lower case due to case-sensitivity
const isMatch = product.toLowerCase().includes(searchQuery.toLowerCase());
console.log(`Case-insensitive match for '${searchQuery}':`, isMatch); // true


// --- 4. Coercion & Omission Quirks ---
const numbers = "123456";
console.log("Coerced number search:", numbers.includes(45)); // true (45 becomes "45")

const text = "This value is undefined";
console.log("Implicit undefined search:", text.includes()); // true ("undefined" is found)
```

---

## Related
* [[js-string-indexof]] - Locating substring positions.
* [[js-string-startswith]] - Checking if a string starts with a substring.
* [[js-string-endswith]] - Checking if a string ends with a substring.
* [[js-string-immutability]] - Primitive string value updates.
* [[MOC - JS Built-in Objects & Utilities]] - Central hub for JavaScript built-in functions.
