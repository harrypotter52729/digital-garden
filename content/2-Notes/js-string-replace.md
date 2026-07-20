---
id: 202607201924
title: "String Modification: replace"
aliases:
  - string-replace
  - replace
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
date_created: 2026-07-20
mastery_level: 1
---

# String Modification: replace

> **TL;DR:** `replace()` is like using a **censor pen to scribble out the first mistake in a paragraph** (it finds and replaces only the **very first occurrence** when given a simple text search, leaving other identical matches completely untouched).

## Purpose & Behavior
The `replace()` method searches a string for a specified value (either a literal string or a Regular Expression) and returns a new string with the first matched occurrence replaced.

```javascript
string.replace(searchValue, replaceValue)
```

* **`searchValue`**: The substring or regular expression pattern you want to replace.
* **`replaceValue`**: The string (or replacement function) to substitute into the match.

---

## Technical Details & Mechanics

### 1. The Single Replacement Trap
The most common mistake when using `replace()` with a string argument is expecting it to replace all matches.
* **String Arguments:** If `searchValue` is a string, only the **first match** is replaced.
  ```javascript
  "cat cat cat".replace("cat", "dog"); // "dog cat cat"
  ```
* **Why it was designed this way:** Historically, `replace()` was created to perform single replacements on plain text. To match and replace all occurrences, JavaScript relied on Regular Expressions with the global (`g`) flag.

### 2. Global Replacement via RegExp
To replace all matches using `replace()`, you must pass a Regular Expression with the global flag (`g`):
```javascript
"cat cat cat".replace(/cat/g, "dog"); // "dog dog dog"
```
You can also combine flags (e.g. `/cat/gi` to search globally and ignore case).

### 3. V8 Engine Allocations & Immutability
Because strings are [[js-string-immutability|immutable]], `replace()` does not edit the original string. It creates and returns a brand new string value.
* **Engine Optimization:** If `searchValue` is not found, the ECMAScript spec dictates that the original string is returned. Modern engines (such as V8) optimize this by returning the original string reference directly without allocating new memory.

---

## Edge Cases & Quirks

* **Empty String Search:** 
  * `"abc".replace("", "X")` returns `"Xabc"`.
  * The engine searches for the first empty boundary, which is located at index `0` (before `"a"`), replacing it with `"X"`.
* **Value Not Found:** `"abc".replace("z", "X")` returns the original string `"abc"`.
* **Type Coercion:** Arguments are coerced. `"12345".replace(23, "XX")` becomes `"12345".replace("23", "XX")` $\rightarrow$ `"12XX45"`.

---

## Canonical Code Example

Here is a script demonstrating single vs global replacements, regex patterns, placeholder replacements, and memory behavior:

```javascript
// --- 1. Literal Single Replacement vs. Regex Global ---
const text = "cat cat cat";

// String searchValue only replaces the first match
const singleReplace = text.replace("cat", "dog");
console.log("String single replacement:", singleReplace); // "dog cat cat"

// RegExp with /g flag replaces all matches
const globalReplace = text.replace(/cat/g, "dog");
console.log("RegExp global replacement:", globalReplace); // "dog dog dog"


// --- 2. Pattern Matching with Regex ---
const phone = "123-456-789";
// Replace all digits (\d) with asterisks
const maskedPhone = phone.replace(/\d/g, "*");
console.log("Masked phone pattern:", maskedPhone); // "***-***-***"


// --- 3. Template Placeholder Substitution ---
const template = "Hello {name}!";
const greetings = template.replace("{name}", "Luffy");
console.log("Placeholder replaced:", greetings); // "Hello Luffy!"


// --- 4. Empty String Boundary and Immutability check ---
let greeting = "Hello";
greeting.replace("H", "Y"); // Returns "Yello", but it is discarded!
console.log("Original string remains unchanged:", greeting); // "Hello" (unmutated)

const insertedBoundary = "abc".replace("", "X");
console.log("Empty string boundary insertion:", insertedBoundary); // "Xabc"
```

---

## Related
* [[js-string-replaceall]] - Standard method to replace all occurrences of a string without RegExp.
* [[js-string-immutability]] - Detailed study on immutable primitive values.
* [[js-string-match]] - Using RegExp to extract matches.
* [[MOC - JS Built-in Objects & Utilities]] - Central MOC index for standard utility methods.
