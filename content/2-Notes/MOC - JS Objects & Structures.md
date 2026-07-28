---
id: 202602081005
title: MOC - JS Objects & Structures
aliases: [JS Objects MOC]
tags:
  - type/MOC
  - lang/js
  - status/processing
  - topic/objects
date_created: 2026-07-28
mastery_level: 1
---
# MOC - JS Objects & Structures

This map acts as a hub for understanding how JavaScript constructs, unpacks, and manages complex data structures, object internals, and metaprogramming features.

## Object Fundamentals & Memory
* [[js-objects-vs-primitives]] — Objects vs primitives and primitive auto-boxing.
* [[js-objects-reference-types]] — Memory allocation, stack pointers, and pass-by-reference semantics.
* [[js-object-identity]] — Reference equality vs structural equality.
* [[js-destructuring]] — Unpacking values from arrays or properties from objects into distinct variables.

## Object Creation Patterns
* [[js-object-creation]] — Syntax and patterns for creating objects.
* [[js-object-create-null-prototype]] — `Object.create()` and null-prototype dictionary objects.
* [[js-constructor-functions]] — Constructor functions and `new` operator mechanics.
* [[js-es6-classes-basics]] — ES6 Class fundamentals and syntactic sugar.
* [[js-class-inheritance-extends-super]] — Class inheritance via `extends` and `super`.
* [[js-composition-vs-inheritance]] — Architectural comparison: Composition vs Inheritance.

## Property Access & Descriptor Metadata
* [[js-object-creation|Dot vs Bracket Notation]] — Property access mechanics and dynamic key access.
* [[js-optional-chaining-operator]] — Safe property chaining with `?.`.
* [[js-property-descriptors]] — Data Descriptors vs Accessor Descriptors.
* [[js-getters-and-setters]] — Accessor properties: getters and setters.

## Object Protection & Deep Cloning
* [[js-object-freeze]] — `Object.freeze()` and recursive `deepFreeze()`.
* [[js-object-freeze-vs-seal]] — Immutability controls (`freeze`, `seal`, `preventExtensions`).
* [[js-shallow-vs-deep-copy]] — Shallow vs deep copying mechanics.
* [[js-structured-clone]] — Deep cloning via `structuredClone()`.

## Prototype Chain & Execution Context
* [[js-prototype-chain-mechanics]] — Prototypal inheritance and `[[js-prototype-chain-mechanics|Prototype]]` lookup.
* [[js-the-this-keyword-rules]] — `this` binding rules overview.
* [[js-this-method-invocation]] — Implicit `this` binding in method calls.
* [[js-this-function-invocation-strict]] — Default `this` binding in regular function calls and strict mode.
* [[js-this-constructor-invocation]] — `new` keyword `this` binding in constructors.
* [[js-this-arrow-functions]] — Lexical `this` resolution in arrow functions.
* [[js-explicit-binding-call-apply-bind]] — Explicit context binding via `call()`, `apply()`, and `bind()`.

## V8 Engine Internals & Performance
* [[js-v8-hidden-classes-shapes]] — V8 hidden classes, shapes, and transition chains.
* [[js-v8-inline-caches]] — Inline caching states (Monomorphic, Polymorphic, Megamorphic).
* [[js-v8-fast-properties-vs-dictionary-mode]] — Fast in-object properties vs dictionary mode demotion.
* [[js-v8-deoptimization-pitfalls]] — JIT compiler deoptimization hazards and bailout triggers.

## Metaprogramming, Reflection & Proxies
* [[js-the-symbol-primitive]] — Symbol primitive type and uniqueness.
* [[js-symbol-primitive-uniqueness]] — Deep dive into Symbol uniqueness and keys.
* [[js-global-symbol-registry]] — Global Symbol Registry (`Symbol.for()` and `keyFor()`).
* [[js-well-known-symbols]] — Language hooks (`Symbol.iterator`, `toPrimitive`, `toStringTag`).
* [[js-reflect-api]] — Standardized object operations via the Reflect API.
* [[js-proxy-api-foundations]] — Proxy API foundations (`get` and `set` traps).
* [[js-proxy-advanced-traps]] — Advanced Proxy traps (`has`, `deleteProperty`, `ownKeys`, `apply`).

## Data Structure Selection & Interchange
* [[js-map-vs-object]] — Detailed structural comparison between Map and Object.
* [[js-weakmap-garbage-collection]] — WeakMap mechanics and automatic garbage collection.
* [[js-weakset-object-tracking]] — WeakSet and memory-safe object tracking.
* [[js-json-serialization-parsing]] — JSON serialization (`stringify`/`parse`) with replacer and reviver.

---
**Related MOCs:**
* [[MOC - JS Data Types & Memory]] (How objects are stored in the Heap)
* [[MOC - JS Built-in Objects & Utilities]] (Standard built-in object utilities)
* [[MOC - JS Advanced Mechanics & Metaprogramming]] (Metaprogramming and engine internals)

* [[js-algorithm-deep-merge]] — Deep merging object trees recursively.
* [[js-object-group-by]] — Grouping array items into object dictionary keys via `Object.groupBy()`.
* [[js-object-is-same-value]] — SameValue equality algorithm via `Object.is()`.
