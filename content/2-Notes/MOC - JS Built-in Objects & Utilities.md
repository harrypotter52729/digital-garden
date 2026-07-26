---
id: 202606120853
title: MOC - JS Built-in Objects & Utilities
aliases: [JS Utilities MOC, Built-in Objects MOC]
tags:
  - type/MOC
  - lang/js
  - status/processing
  - topic/objects
date_created: 2026-06-12
mastery_level: 1
---
# MOC - JS Built-in Objects & Utilities

This map focuses on JavaScript's standard built-in objects, serialization helpers, and math specifications used to format, parse, and manipulate data.

## Serialization & Time
* [[js-json-parse-and-stringify]] - Freezing and hydrating object structures into strings.
* [[js-the-date-object-and-quirks]] - Managing epoch times, timezones, and zero-indexed months.

## Object Operations
* [[js-object-keys-values-entries]] - Extracting arrays of keys, values, or entries from objects.
* [[js-object-freeze-vs-seal]] - Applying varying levels of immutability to object configurations.

## Array Operations & Data Structures
* [[js-array-specialized-objects]] - Array object model, property keys, and object inheritance.
* [[js-array-sparse-vs-dense-holes]] - Dense vs sparse arrays and empty slots (holes) vs undefined.
* [[js-array-length-mechanics]] - Array length property, automatic updates, and truncation mechanics.
* [[js-array-creation-methods]] - Creation via `[]`, `Array()`, `Array.of()`, and `Array.from()`.
* [[js-array-access-at-and-optional-chaining]] - Indexing, `at()`, and optional chaining `?.[]`.
* [[js-array-push-pop-shift-unshift-splice]] - Mutating element operations, memory shifts, and complexity.
* [[js-array-methods-mutating-vs-non-mutating]] - Complete reference of mutating vs non-mutating array operations.
* [[js-array-modern-immutable-methods]] - Modern ES2023 immutable methods (`toSorted`, `toReversed`, `toSpliced`, `with`).
* [[js-array-foreach-vs-map-filter]] - Functional iteration semantics (`forEach`, `map`, `filter`).
* [[js-array-reduce-and-reduceright]] - Aggregation via `reduce` and `reduceRight`.
* [[js-array-flat-and-flatmap]] - Array flattening (`flat`, `flatMap`).
* [[js-array-searching-includes-vs-indexof-find]] - Primitive and object search APIs (`includes`, `indexOf`, `find`, `findLast`).
* [[js-array-holes-in-methods]] - Hole handling algorithms across ES5 vs ES6+ ECMAScript built-ins.
* [[js-array-iterators-keys-values-entries]] - Array iterators (`keys`, `values`, `entries`) and iteration protocols.
* [[js-array-typed-arrays-and-arraybuffer]] - `ArrayBuffer`, `TypedArray`, and `DataView` binary memory.
* [[js-array-algorithmic-patterns]] - Deduplication, frequency counting, two pointers, sliding window, and prefix sums.

## Mathematical Precision
* [[js-floating-point-math-quirks]] - Working around V8 float rounding limits.

## String Operations

### Searching & Matching
* [[js-string-autoboxing]] - Wrapping primitive strings in temporary objects.
* [[js-string-character-access]] - Internal access speeds of bracket notation vs. charAt.
* [[js-string-at]] - Accessing characters with negative indexes.
* [[js-string-includes]] - Checking substring presence returning a boolean.
* [[js-string-indexof]] - Finding the first index occurrence of a substring.
* [[js-string-lastindexof]] - Finding the last index occurrence by searching backward.
* [[js-string-startswith]] - Verifying string prefix values.
* [[js-string-endswith]] - Checking string suffix values.
* [[js-string-search]] - Finding the index position of a regex match.
* [[js-string-match]] - Retrieving first or global matches using regular expressions.
* [[js-string-matchall]] - Obtaining an iterator for detailed matches and groups.

### Extraction & Modification
* [[js-string-slice]] - Extracting substring slices with negative offset support.
* [[js-string-substring]] - Extracting substrings with index auto-swapping and negative-to-0 clamping.
* [[js-string-slice-vs-substring-vs-substr]] - Deciding between standard extraction APIs and legacy substr.
* [[js-string-split]] - Splitting strings into arrays of substrings.
* [[js-string-replace]] - Replacing the first match of a string or regex.
* [[js-string-replaceall]] - Swapping out all matches of a substring or global regex.

### Formatting, Padding & Internals
* [[js-string-trim]] - Removing surrounding whitespace from string boundaries.
* [[js-string-case-conversion]] - Normalizing casing using Unicode-aware rules.
* [[js-string-concat]] - Combining strings and the mechanics of V8 rope/cons strings.
* [[js-string-repeat]] - Multiplying string values with truncation and range checks.
* [[js-string-padstart-padend]] - Aligning strings to a target width.
* [[js-string-charcodeat-vs-codepointat]] - Reading 16-bit code units vs full Unicode code points.
* [[js-string-localecompare]] - Sorting and comparing strings according to locale-specific collation rules.
* [[js-string-normalize]] - Standardizing visual character mappings via Unicode composition/decomposition.
* [[js-string-valueof-and-tostring]] - Wrapper object unwrapping and type coercion conversions.

---
**Related MOCs:**
* [[MOC - JavaScript Core]]
* [[MOC - JS Objects & Structures]]
