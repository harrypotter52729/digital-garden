---
id: 202607280841
title: MOC - JS Engine Internals & V8 Performance
aliases:
  - JS Engine Internals MOC
  - V8 Performance MOC
tags:
  - type/MOC
  - lang/js
  - status/processing
  - topic/v8-internals
  - topic/performance
date_created: 2026-07-28
mastery_level: 1
---

# MOC - JS Engine Internals & V8 Performance

This map acts as a central hub for understanding how JavaScript engines (primarily V8) optimize objects, compile machine code, manage memory, and handle garbage collection under the hood.

## Memory Allocation & Storage
* [[js-stack-vs-heap-memory]] — Call stack execution frames vs Heap object allocation.
* [[js-objects-reference-types]] — Reference semantics and pointer address storage.
* [[js-garbage-collection-mark-and-sweep]] — V8 Mark-and-Sweep garbage collection algorithms.
* [[js-weakmap-garbage-collection]] — Weak references and memory leak prevention.

## V8 Hidden Classes & Shape Transitions
* [[js-v8-hidden-classes-shapes]] — V8 Hidden Classes, shape metadata, and transition chains.
* [[js-v8-fast-properties-vs-dictionary-mode]] — Fast In-Object/Out-of-Object slots vs Slow Dictionary Mode.
* [[js-v8-deoptimization-pitfalls]] — JIT compiler deoptimization hazards, `delete` impact, and bailout triggers.

## JIT Compilation & Inline Caching
* [[js-v8-inline-caches]] — Inline Cache (IC) states: Monomorphic, Polymorphic, and Megamorphic.

---
**Related MOCs:**
* [[MOC - JS Objects & Structures]] (Central Objects Hub)
* [[MOC - JS Data Types & Memory]] (Memory Architecture Hub)

* [[js-ecmascript-internal-slots]] — ECMAScript spec internal slots (`[[js-ecmascript-internal-slots|[[Construct]]]]`, `[[js-ecmascript-internal-slots|[[Get]]]]`, `[[js-ecmascript-internal-slots|[[Set]]]]`, `[[js-ecmascript-internal-slots|[[Extensible]]]]`).
