
---
id: 202602081403
title: Prototypal Inheritance
aliases: [Prototypes, Prototype Chain]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/oop
date_created: {{date}}
mastery_level: 1
---
# Prototypal Inheritance

JavaScript does not have traditional "classes" like Java or C++. Instead, it uses a system called **Prototypal Inheritance** (often called Behavior Delegation). Objects inherit directly from other objects.

## What is a Prototype?
Every JavaScript object has a hidden, internal property called `[[js-prototype-chain-mechanics|Prototype]]` (historically accessed via `__proto__`). This is simply a reference (a memory link) to another object. 

## The Prototype Chain (Property Lookup)
When you try to access a property or method on an object:
1. The JS Engine looks on the object itself.
2. If it doesn't find it, it follows the `[[js-prototype-chain-mechanics|Prototype]]` link to the parent object and looks there.
3. If not found, it checks the parent's parent, continuing up the **Prototype Chain**.
4. The chain ends at `Object.prototype`. If the property still isn't found, it returns `null` or `undefined`.

```javascript
const animal = { eats: true };
const rabbit = { jumps: true };

// Setting the rabbit's prototype to be 'animal' (Legacy way)
rabbit.__proto__ = animal; 

console.log(rabbit.jumps); // true (found on rabbit)
console.log(rabbit.eats);  // true (not on rabbit, found via prototype chain)
```

## `prototype` vs `__proto__`

This is a notorious source of confusion:

- `__proto__` (or `Object.getPrototypeOf()`) is the actual link pointing to an object's parent.
- `.prototype` is a special property that _only exists on constructor functions_. It is the object that will be assigned as the `__proto__` for all instances created with that constructor via the `new` keyword.

## Related

- [[js-object-creation]]
- [[js-es6-classes-under-the-hood]] - How modern syntax hides this prototype chain.