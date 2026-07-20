---
id: 202607201934
title: "Regex Matching: matchAll"
aliases:
  - string-matchall
  - matchall
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/strings
  - topic/regex
date_created: 2026-07-20
mastery_level: 1
---

# Regex Matching: matchAll

> **TL;DR:** `matchAll()` is like receiving **one book at a time from a librarian** (instead of allocating a huge pile of matching results in memory immediately, it returns a **lazy iterator** that yields a detailed match object for every single match).

## Purpose & Behavior
The `matchAll()` method returns an iterator of all results matching a string against a regular expression, including capturing groups.

```javascript
string.matchAll(regexp)
```

* **`regexp`**: A regular expression object. It **must** have the global (`g`) flag, or a `TypeError` will be thrown.

---

## Technical Details & Mechanics

### 1. Why `matchAll()` Was Created
Before ES2020, if you wanted to find all matches of a pattern *and* get their capturing groups or match index offsets, [[js-string-match|match()]] failed because calling `match(/pattern/g)` discards all metadata (returning a simple array of matched substrings). 

Developers had to write complex loops using `RegExp.prototype.exec()`. `matchAll()` resolves this by returning detailed match objects for *every* match in a single operation.

### 2. The Global RegExp Requirement
Similar to [[js-string-replaceall|replaceAll()]], you **must** use the global (`g`) flag on your regular expression. If you omit it, JavaScript throws an exception:
```javascript
"abc".matchAll(/abc/); // TypeError: String.prototype.matchAll called with a non-global RegExp
```

### 3. Returns an Iterator (Lazy Evaluation)
Unlike other methods, `matchAll()` does not return an array. It returns a **RegExp String Iterator**.
* **Memory Efficiency:** If you are scanning a 2 GB log file, returning an array of millions of matches would consume an enormous amount of memory. An iterator evaluates lazily—meaning the engine only processes the next match when you request it.
* **Exhaustion:** Iterators are one-way. Once you loop through the matches, the iterator is **exhausted** and cannot be restarted. To loop again, you must recreate the iterator or convert it to an array.

---

## Accessing Capturing Groups
For every yielded match, you receive an array containing:
* Index `0`: The full match text.
* Index `1` to `n`: Captured group values.
* `.index`: Starting position of the match.

---

## Canonical Code Example

Here is a copy-pasteable script demonstrating iteration, capture group extraction, array spreading, and exhaustion:

```javascript
// --- 1. Basic Iteration with for...of ---
const sentence = "John:25 Alice:30";
// Regex captures: (\w+) = name, (\d+) = age
const regex = /(\w+):(\d+)/g;

const matches = sentence.matchAll(regex);

console.log("Lazy Iteration results:");
for (const match of matches) {
  console.log(`- Full match: ${match[0]}`);
  console.log(`  Name (Group 1): ${match[1]}`);
  console.log(`  Age (Group 2): ${match[2]}`);
  console.log(`  Start Index: ${match.index}`);
}


// --- 2. Exhaustion Quirks ---
// Attempting to loop over the 'matches' iterator again will yield nothing
console.log("Iterating exhausted matches iterator:");
for (const match of matches) {
  console.log("This will never print!");
}


// --- 3. Spreading Iterator into a Normal Array ---
const freshMatches = sentence.matchAll(regex);
const matchesArray = [...freshMatches];

console.log("Spread array representation:", matchesArray);
console.log("Second match's age:", matchesArray[1][2]); // "30"


// --- 4. RegExp Error Handling ---
try {
  "Java Java".matchAll(/Java/); // Omitted 'g' flag
} catch (error) {
  console.log("Regex Error caught:", error.message);
  // "String.prototype.matchAll called with a non-global RegExp"
}
```

---

## Related
* [[js-string-match]] - Getting simple arrays of matches or single detailed match.
* [[js-string-replaceall]] - Replacing all matches in a string.
* [[js-generators-and-iterators]] - Deep dive into JavaScript iterators and generators.
* [[MOC - JS Built-in Objects & Utilities]] - Central MOC index for standard utility methods.
