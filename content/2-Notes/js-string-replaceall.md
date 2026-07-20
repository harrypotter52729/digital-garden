---
id: 202607201925
title: "String Modification: replaceAll"
aliases:
  - string-replaceall
  - replaceall
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
date_created: 2026-07-20
mastery_level: 1
---

# String Modification: replaceAll

> **TL;DR:** `replaceAll()` is like using a **find-and-replace command in a word processor** (it automatically swaps out **every single occurrence** of your search term without forcing you to write regular expressions).

## Purpose & Behavior
The `replaceAll()` method returns a new string with all matches of a pattern replaced by a replacement. The original string remains unmodified.

```javascript
string.replaceAll(searchValue, replaceValue)
```

* **`searchValue`**: The substring or regular expression pattern to replace.
* **`replaceValue`**: The string (or replacement function) to substitute for matches.

---

## Technical Details & Mechanics

### 1. Why `replaceAll()` Was Created (ES2021)
Before ECMAScript 2021, if developers wanted to replace all occurrences of a word like `"cat"`, they could not write `str.replace("cat", "dog")` because that only replaces the first match. They were forced to write:
```javascript
str.replace(/cat/g, "dog");
```
This introduced unnecessary complexity (forcing developers to learn regular expression syntax and escape special characters). 

Changing the default behavior of `replace()` to replace all matches would have broken millions of legacy websites. Therefore, TC39 added `replaceAll()` as a distinct method.

### 2. The Regular Expression Global Rule
If you pass a Regular Expression as the `searchValue` to `replaceAll()`, **it must include the global (`g`) flag**.
* If the `g` flag is omitted, JavaScript throws a **`TypeError`**:
  ```javascript
  "cat cat".replaceAll(/cat/, "dog"); // TypeError: String.prototype.replaceAll called with a non-global RegExp
  ```
* **Why:** `replaceAll()` promises to replace *all* occurrences. A non-global regex is only capable of matching a single occurrence. Rather than silently executing the wrong action, the engine throws an error to prevent bugs.

---

## Edge Cases & Quirks

* **Empty String Search:**
  * `"abc".replaceAll("", "-")` returns `"-a-b-c-"`.
  * Because `replaceAll()` targets *every* match, it replaces all 4 empty boundaries in the string (before `"a"`, between characters, and after `"c"`).
  * *(Note: In contrast, [[js-string-replace|replace()]] only replaces the first boundary, returning `"-abc"`).*
* **Loop Replacement Anti-Pattern:**
  Avoid replacing all matches using a loop like this:
  ```javascript
  while (text.includes("cat")) {
    text = text.replace("cat", "dog"); // Extremely inefficient!
  }
  ```
  This repeatedly scans the string. A single `replaceAll("cat", "dog")` is much more efficient.

---

## Canonical Code Example

Here is a copy-pasteable script highlighting string replacement, regex safety checks, boundary values, and error scenarios:

```javascript
// --- 1. Basic String Replacement ---
const sentence = "I like Java. Java is fun.";
const updatedSentence = sentence.replaceAll("Java", "JavaScript");
console.log("Replaced all strings:", updatedSentence); // "I like JavaScript. JavaScript is fun."


// --- 2. Real World: Formatting Path Separators & Text Cleaning ---
const windowsPath = "folder\\sub\\file.txt";
const unixPath = windowsPath.replaceAll("\\", "/");
console.log("Windows path to Unix:", unixPath); // "folder/sub/file.txt"

const comment = "This app is bad. Really bad.";
const cleanComment = comment.replaceAll("bad", "***");
console.log("Censored text:", cleanComment); // "This app is ***. Really ***."


// --- 3. Regex Global Error Trap ---
try {
  "cat cat".replaceAll(/cat/, "dog");
} catch (error) {
  console.log("Regex Error caught:", error.message); 
  // "String.prototype.replaceAll called with a non-global RegExp"
}

// Correct RegExp usage:
const validRegex = "cat cat".replaceAll(/cat/g, "dog");
console.log("Correct regex replacement:", validRegex); // "dog dog"


// --- 4. Empty String Boundary replacement ---
const boundaryReplace = "abc".replaceAll("", "*");
console.log("Empty boundaries replaced:", boundaryReplace); // "*a*b*c*"
```

---

## Related
* [[js-string-replace]] - Replacing only the first occurrence of a string.
* [[js-string-split]] - Splitting strings into array segments.
* [[js-string-immutability]] - String values cannot be modified in place.
* [[MOC - JS Built-in Objects & Utilities]] - Central directory for utility operations.
