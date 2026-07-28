---
id: 202607282005
title: ECMAScript Internal Slots & Spec Operations
aliases:
  - ECMAScript internal slots
  - internal slots js
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/v8-internals
  - topic/objects
date_created: 2026-07-28
mastery_level: 1
---

# ECMAScript Internal Slots & Spec Operations

> **TL;DR:** Internal Slots (denoted with double square brackets like `[[Construct]]` or `[[Get]]`) are invisible internal engine attributes and methods defined by the ECMAScript Language Specification—they govern the exact runtime behavior of JavaScript objects under the hood!

---

## What Are Internal Slots?
Internal slots are structural attributes used by the ECMAScript specification to define object state and behavior. They are **not object properties** and are not inherited or accessible directly via JavaScript code (unless exposed via reflective APIs like `Reflect` or `Object.getPrototypeOf`).

---

## Major ECMAScript Internal Slots & Operations

### 1. `[[Construct]]`
- **Role:** Executes object allocation and constructor code when invoked via `new`.
- **Behavior:** Functions possessing `[[Construct]]` are constructors. Arrow functions lack `[[Construct]]` and throw a `TypeError` if invoked with `new`.

### 2. `[[Get]]` (Property Retrieval)
- **Role:** Internal algorithm executed when evaluating `obj.prop`.
- **Behavior:** Checks own properties, triggers getter functions if defined, or recursively traverses ``[[Prototype]]`` until `null`.

### 3. `[[Set]]` (Property Assignment)
- **Role:** Internal algorithm executed when evaluating `obj.prop = value`.
- **Behavior:** Triggers setters on own or prototype objects, or invokes `[[DefineOwnProperty]]`.

### 4. `[[DefineOwnProperty]]`
- **Role:** Creates or updates a property descriptor directly on an object.

### 5. `[[Extensible]]`
- **Role:** Boolean flag determining whether new properties can be added to the object.
- **Behavior:** Set to `false` by `Object.preventExtensions()`, `Object.seal()`, or `Object.freeze()`.

### 6. `[[PrimitiveValue]]`
- **Role:** Internal slot on primitive wrapper objects (`String`, `Number`, `Boolean`, `Symbol`) holding the raw primitive value.

---

## Related
- [[js-prototype-chain-mechanics]] — Prototype traversal via `[[Get]]` and ``[[Prototype]]``.
- [[js-property-descriptors]] — Property descriptor metadata.
- [[MOC - JS Objects & Structures]] — Central Objects MOC.
