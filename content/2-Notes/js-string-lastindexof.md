---
id: 202607201917
title: "String Searching: lastIndexOf"
aliases:
  - string-lastindexof
  - lastindexof
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
date_created: 2026-07-20
mastery_level: 1
---

# String Searching: lastIndexOf

> **TL;DR:** `lastIndexOf()` is like scanning a book **backward from the last page** to find the last time a word was used, but still referencing the **normal forward index** for its position.

## Purpose & Behavior
The `lastIndexOf()` method searches the string backward, returning the index of the last occurrence of the specified substring. If the substring is not found, it returns `-1`.

```javascript
string.lastIndexOf(searchString, fromIndex)
```

* **`searchString`**: The substring you are searching for.
* **`fromIndex` (Optional)**: The index from which to start searching backward. Defaults to `+Infinity` (effectively searching the entire string).

---

## Technical Details & Mechanics

### 1. Reverse Traversal, Forward Indexes
Although `lastIndexOf()` searches **right-to-left** (backward), the returned index is always a **normal forward index** (0-indexed from the start). It never returns a negative index relative to the end.

To optimize this, JavaScript engines do not copy and reverse the string (strings are [[js-string-immutability|immutable]]). Instead, the engine changes the direction of character traversal (e.g., matching indices $5 \rightarrow 4 \rightarrow 3 \rightarrow 2 \rightarrow 1 \rightarrow 0$).

### 2. The `fromIndex` Boundary
`fromIndex` establishes the starting point of the backward search. It tells the engine: **"Do not search to the right of this index."**
* `"banana".lastIndexOf("a", 3)` starts searching backward from index `3`. It checks indices `3`, `2`, `1`, `0` and finds the last `"a"` at index `3`.
* `"banana".lastIndexOf("a", 0)` starts at index `0` ("b"). Since there are no occurrences of "a" at index 0 and it cannot move left, it returns `-1`.

> [!WARNING]
> A common point of confusion is when the matched substring extends past `fromIndex`. As long as the match *starts* at or before `fromIndex`, it is valid. For example:
> `"banana".lastIndexOf("ana", 1)` returns `1`, because the match starts at index `1`, even though the letters "n" and "a" exist at indices 2 and 3.

---

## Edge Cases & Quirks

* **Empty String Lookup:** 
  * `"hello".lastIndexOf("")` returns `5` (the string's length).
  * `"hello".lastIndexOf("", 3)` returns `3`.
  * The empty string is considered to match at the last empty position, which resides after the final character (index = `str.length`).
* **Out-of-Bounds `fromIndex`:**
  * If `fromIndex` $\ge$ `str.length`, the entire string is searched.
  * If `fromIndex` $< 0$, it is clamped to `0`, meaning only index `0` is checked.
* **Type Coercion:** Arguments are coerced to strings.
  * `"12345".lastIndexOf(23)` becomes `"12345".lastIndexOf("23")`, returning `1`.

---

## Real-World Applications

A very common production pattern is finding directory separators or file extensions:

```javascript
const filePath = "/users/luffy/documents/resume.backup.pdf";

// Find the last dot to locate the extension
const lastDot = filePath.lastIndexOf(".");
const extension = filePath.slice(lastDot + 1); // "pdf"

// Find the last slash to separate the folder path from the filename
const lastSlash = filePath.lastIndexOf("/");
const filename = filePath.slice(lastSlash + 1); // "resume.backup.pdf"
```

---

## Canonical Code Example

Here is a copy-pasteable script highlighting edge cases and behavior:

```javascript
// --- 1. Basic backward search ---
const word = "banana";
console.log("Last 'a' index:", word.lastIndexOf("a")); // 5 (at the end)
console.log("Last 'na' index:", word.lastIndexOf("na")); // 4 (starts at index 4)


// --- 2. Demonstrating the fromIndex Boundary ---
// "b a n a n a"
//  0 1 2 3 4 5
console.log("Search 'a' from index 2:", word.lastIndexOf("a", 2)); // 1 (checks indices 2, 1, 0)
console.log("Search 'a' from index 0:", word.lastIndexOf("a", 0)); // -1 (checks index 0 only)

// Traps: Starts before but extends past fromIndex
console.log("Search 'ana' from index 1:", word.lastIndexOf("ana", 1)); // 1 (valid, starts at 1)


// --- 3. Empty String Behavior ---
const str = "hello";
console.log("Empty string match:", str.lastIndexOf("")); // 5 (equal to str.length)
console.log("Empty string match from 3:", str.lastIndexOf("", 3)); // 3


// --- 4. Out-of-Bounds clamping ---
console.log("Clamped negative fromIndex:", str.lastIndexOf("h", -10)); // 0 (negative clamped to 0)
console.log("Excessive fromIndex:", str.lastIndexOf("o", 100)); // 4 (searches whole string)
```

---

## Related
* [[js-string-indexof]] - Finding the first index of a substring.
* [[js-string-slice]] - Using indexes to extract sections of strings.
* [[js-string-endswith]] - Checking suffix matches.
* [[MOC - JS Built-in Objects & Utilities]] - Central MOC index for built-in object helpers.
