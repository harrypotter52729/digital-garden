---
id: 202602081002
title: MOC - JS Data Types & Memory
aliases: [JS Types MOC, JS Memory MOC]
tags:
  - type/MOC
  - lang/js
  - status/processing
  - topic/data-types
date_created: {{date}}
mastery_level: 1
---
# MOC - JS Data Types & Memory

This map focuses on the nature of JavaScript's type system and how different data types interact with the computer's memory (Stack vs. Heap).

## The Type System
* [[js-weak-typing-and-dynamic-types]] - Understanding JS as a dynamically and weakly typed language.
* [[js-null-vs-undefined]] - The difference between an intentionally empty value and an uninitialized one.

## Memory & References
* [[js-primitive-vs-reference-types]] - How primitives are stored by value (Stack) and objects by reference (Heap).
* [[js-shallow-vs-deep-copy]] - The implications of reference types when copying data and how to avoid accidental mutations.
* [[js-array-sparse-vs-dense-holes]] - Packed vs holey array representations in V8 memory.
* [[js-array-length-mechanics]] - Dynamic allocation and heap truncation mechanics.
* [[js-array-typed-arrays-and-arraybuffer]] - Raw binary memory allocations (`ArrayBuffer`, `TypedArray`, `DataView`).

## Strings & Memory
* [[js-string-fundamentals]] - Strings as ordered sequences of UTF-16 code units.
* [[js-string-immutability]] - Why string primitives cannot be changed after creation.
* [[js-string-memory-storage]] - String interning, reference sharing, and Rope string engine layouts.

---
**Related MOCs:**
* [[MOC - JS Type Conversion & Equality]] (How these types interact when compared)