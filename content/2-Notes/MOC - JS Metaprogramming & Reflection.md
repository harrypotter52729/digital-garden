---
id: 202607280842
title: MOC - JS Metaprogramming & Reflection
aliases:
  - JS Metaprogramming MOC
  - Reflection MOC
tags:
  - type/MOC
  - lang/js
  - status/processing
  - topic/metaprogramming
  - topic/proxy-api
date_created: 2026-07-28
mastery_level: 1
---

# MOC - JS Metaprogramming & Reflection

This map acts as a central hub for dynamic language features, reflection, object interception, and language-level customization hooks.

## Symbol Primitive & Metaprogramming Hooks
* [[js-symbol-primitive-uniqueness]] — Symbol primitive type, uniqueness, and property keys.
* [[js-global-symbol-registry]] — Global Symbol Registry (`Symbol.for()` and `Symbol.keyFor()`).
* [[js-well-known-symbols]] — Language hooks (`Symbol.iterator`, `Symbol.toPrimitive`, `Symbol.toStringTag`).

## The Reflect API
* [[js-reflect-api]] — Standardized functional object operations via the Reflect API namespace.

## The Proxy API & Interception
* [[js-proxy-api-foundations]] — Proxy API foundations (`get` and `set` traps).
* [[js-proxy-advanced-traps]] — Advanced Proxy traps (`has`, `deleteProperty`, `ownKeys`, `apply`).

## Accessor Descriptors & Dynamic Control
* [[js-property-descriptors]] — Property metadata (Data vs Accessor Descriptors).
* [[js-getters-and-setters]] — Accessor properties: getters and setters.

---
**Related MOCs:**
* [[MOC - JS Objects & Structures]] (Central Objects Hub)
* [[MOC - JS Advanced Mechanics & Metaprogramming]] (Advanced Mechanics Hub)
