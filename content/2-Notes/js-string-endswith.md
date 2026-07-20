---
id: 202607201919
title: "String Searching: endsWith"
aliases:
  - string-endswith
  - endswith
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
date_created: 2026-07-20
mastery_level: 1
---

# String Searching: endsWith

> **TL;DR:** `endsWith()` is like checking the **file extension label on a document** (you inspect only the final characters to verify if it matches, ignoring the rest of the text).

## Purpose & Behavior
The `endsWith()` method determines whether a string ends with the characters of a specified substring, returning `true` or `false` as appropriate.

```javascript
string.endsWith(searchString, length)
```

* **`searchString`**: The suffix substring you are checking for.
* **`length` (Optional)**: The length of the string to consider. If provided, the engine behaves as if the string ends at this length. Defaults to `string.length`.

---

## Technical Details & Mechanics

### 1. The Ribbon and Scissors Analogy (The `length` parameter)
The second parameter `length` is **not a starting index**. Instead, think of the string as a physical ribbon:
1. The engine uses "scissors" to **cut the ribbon** at the specified `length`.
2. It then asks: **"Does this shortened ribbon end with `searchString`?"**

* `"JavaScript".endsWith("Java", 4)` cuts the ribbon at length `4`, resulting in the string `"Java"`. Since `"Java"` ends with `"Java"`, it returns `true`.
* `"JavaScript".endsWith("Script", 7)` cuts the ribbon at length `7`, resulting in `"JavaScr"`. Since `"JavaScr"` does not end with `"Script"`, it returns `false`.

### 2. Efficiency ($O(m)$ Complexity)
Like [[js-string-startswith|startsWith()]], `endsWith()` is highly efficient.
* **Time Complexity:** $O(m)$, where $m$ is the length of `searchString`.
* It calculates the offset where the comparison should begin (`length - searchString.length`) and compares the characters directly.
* It does not perform a search or scan the preceding string characters.
* It does not allocate a new string in memory, maintaining [[js-string-immutability|immutability]] rules.

---

## Edge Cases & Quirks

* **Empty String Search:** 
  * `"hello".endsWith("")` returns `true`.
  * `"hello".endsWith("", 3)` returns `true` because the empty string matches at any valid boundary (such as index 3).
* **Search String Longer Than Considered Length:** `"hi".endsWith("hello")` returns `false`.
* **Type Coercion:** Non-string arguments are coerced to strings.
  * `"12345".endsWith(45)` becomes `"12345".endsWith("45")`, returning `true`.
  * `"OpenAI".endsWith()` searches for `"undefined"`, returning `false`.

---

## Canonical Code Example

Here is a copy-pasteable script highlighting validation patterns and the `length` boundary:

```javascript
// --- 1. Real World: File Extension Validation ---
function isValidImage(filename) {
  // Direct extension check
  return filename.endsWith(".jpg") || filename.endsWith(".png");
}

console.log("Is 'photo.jpg' valid:", isValidImage("photo.jpg")); // true
console.log("Is 'doc.pdf' valid:", isValidImage("doc.pdf"));     // false


// --- 2. Real World: URL Domain Validation ---
const url = "https://openai.com";

if (url.endsWith(".com")) {
  console.log("URL ends with a common commercial domain"); // Prints
}


// --- 3. Demonstrating the Length Parameter ---
const product = "JavaScript";

// Cut ribbon at 4 -> "Java". Ends with "Java"?
console.log("Ends with 'Java' at length 4:", product.endsWith("Java", 4)); // true

// Cut ribbon at 7 -> "JavaScr". Ends with "Script"?
console.log("Ends with 'Script' at length 7:", product.endsWith("Script", 7)); // false


// --- 4. Coercion & Quirks ---
console.log("Omitted argument search:", "abc".endsWith()); // false
console.log("Empty string matching at length 3:", "OpenAI".endsWith("", 3)); // true
```

---

## Related
* [[js-string-startswith]] - Checking prefix matches.
* [[js-string-includes]] - Searching for substrings anywhere in the string.
* [[js-string-indexof]] - Finding first occurrence indexes.
* [[js-string-slice]] - Index-based extraction.
* [[MOC - JS Built-in Objects & Utilities]] - Central MOC index for standard utility methods.
