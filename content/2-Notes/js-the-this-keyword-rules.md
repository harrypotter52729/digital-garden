---
id: 202607280850
title: The this Keyword Binding Rules Overview
aliases:
  - this rules overview
tags:
  - type/redirect
  - lang/js
  - status/processed
  - topic/this-keyword
date_created: 2026-07-28
mastery_level: 1
---

# The `this` Keyword Binding Rules Overview

> **Notice:** This legacy monolithic note has been refactored into dedicated atomic notes corresponding to each call-site binding rule.

## Atomic Notes
- [[js-this-method-invocation]] — Implicit `this` binding in method calls (`obj.method()`).
- [[js-this-function-invocation-strict]] — Default `this` binding in standalone function calls & Strict Mode.
- [[js-this-constructor-invocation]] — `new` keyword `this` binding in constructors and class instantiations.
- [[js-this-arrow-functions]] — Lexical `this` resolution in arrow functions.
- [[js-explicit-binding-call-apply-bind]] — Explicit binding via `call()`, `apply()`, and `bind()`.

---
**Main Hub:** [[MOC - JS Execution Context & this Binding]]