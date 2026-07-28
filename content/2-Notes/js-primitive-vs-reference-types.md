---
id: 202601282127
title: js-memory-model
aliases:
  - primitive vs reference
  - mutable vs immutable
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/memory
date_created: 2026-01-28
date_modified: 2026-01-28
mastery_level: 1
---
## js-primitive-vs-reference-types

### 1. Primitive Types (The Stack) 

Data types that are simple and fixed in size (`string`, `number`, `boolean`, `null`, `undefined`, `symbol`, `bigint`) are stored in the **Stack**.

* they are **immutable**
* **Copying:** Assigned **by value**. `b = a` creates a completely independent copy.
* **Garbage Collection:** When the old value becomes unreachable (orphaned), it is cleared.

### 2. Reference Types (The Heap) 

Complex data types (`objects`, `arrays`, `functions`) are stored in the **Heap** because their size can change dynamically. 
In **Stack** variable it store the reference(address) to the heap data

* **Mutability:** They are mutable.
* **Copying:** Assigned **by reference**. `b = a` copies the *pointer*, not the object. Both variables point to the same address.
* While **comparing** it checks if both objects points to same address or not


## Related

- [[js-shallow-vs-deep-copy]] - How to copy heap objects without sharing references.
- [[js-null-vs-undefined]] - Specifics of empty primitive types.
- [[js-loose-equality-comparison-rules]] - how the objects and primitves  are compared