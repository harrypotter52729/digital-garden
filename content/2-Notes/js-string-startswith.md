---
id: 202607201918
title: "String Searching: startsWith"
aliases:
  - string-startswith
  - startswith
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
date_created: 2026-07-20
mastery_level: 1
---

# String Searching: startsWith

> **TL;DR:** `startsWith()` is like inspecting the **first few letters of a name badge** (you immediately know if it matches the prefix without scanning the rest of the badge).

## Purpose & Behavior
The `startsWith()` method determines whether a string begins with the characters of a specified string, returning `true` or `false` as appropriate.

```javascript
string.startsWith(searchString, position)
```

* **`searchString`**: The prefix substring you are looking for.
* **`position` (Optional)**: The search position. Pretend this position is the beginning of the string. Defaults to `0`.

---

## Technical Details & Mechanics

### 1. The `position` Parameter
Many developers assume `position` means "start searching forward from here". That is incorrect.
Instead, it means: **"Treat this index as index 0 (the start of the string) and perform a direct comparison."**

* `"JavaScript".startsWith("Script", 4)` checks if the substring starting at index `4` begins with `"Script"`. Since index 4 is `"S"`, `"Script"` matches, and it returns `true`.
* `"JavaScript".startsWith("Java", 1)` treats index `1` (`"a"`) as the beginning, checking if `"avaScript"` starts with `"Java"`. This returns `false`.

### 2. Efficiency ($O(m)$ Time Complexity)
Unlike [[js-string-includes|includes()]] or [[js-string-indexof|indexOf()]] which scan through the entire string, `startsWith()` has a time complexity of **$O(m)$**, where $m$ is the length of `searchString`.
* It only compares the exact number of characters needed.
* It stops comparing immediately on the first character mismatch.
* It avoids new string allocations, respecting string immutability.

### 3. Readability & Intent
Before ES6 introduced `startsWith()`, developers had to check prefixes using:
```javascript
if (str.indexOf("prefix") === 0) { ... }
```
This is less readable because it requires several mental steps (calling `indexOf`, returning an index, and comparing it to 0). `startsWith()` communicates intent directly, creating self-documenting code.

---

## Edge Cases & Quirks

* **Empty String Search:** `"hello".startsWith("")` returns `true`. An empty string matches the start of any string.
* **Search String Longer Than Source:** `"hi".startsWith("hello")` returns `false`.
* **Type Coercion:** Non-string arguments are coerced to strings.
  * `"12345".startsWith(12)` returns `true` (coerced to `"12"`).
  * `"OpenAI".startsWith()` searches for `"undefined"`, returning `false`.

---

## Canonical Code Example

Here is a script demonstrating routing validation, file filtering, and comparison efficiency:

```javascript
// --- 1. Real World: API Route Validation ---
const endpoint = "/api/v1/users";

if (endpoint.startsWith("/api")) {
  console.log("Valid API request route detected"); // Prints
}


// --- 2. Real World: Command Parsing ---
const cliInput = "/help server";

if (cliInput.startsWith("/")) {
  console.log("Input is a command"); // Prints
}


// --- 3. Positional comparison ---
const lang = "JavaScript";
// Index 4 of "JavaScript" is "Script"
const hasPrefixFromFour = lang.startsWith("Script", 4);
console.log("Starts with 'Script' at index 4:", hasPrefixFromFour); // true


// --- 4. Coercion and Quirks ---
console.log("Empty string prefix:", "hello".startsWith("")); // true
console.log("Coerced number prefix:", "99-problems".startsWith(99)); // true
console.log("Longer search string prefix:", "hi".startsWith("hello")); // false
```

---

## Related
* [[js-string-endswith]] - Checking suffix matches.
* [[js-string-includes]] - Searching for a substring anywhere in the string.
* [[js-string-indexof]] - Locating first index positions.
* [[js-string-character-access]] - Character retrieval differences.
* [[MOC - JS Built-in Objects & Utilities]] - Central MOC index for standard utility methods.
