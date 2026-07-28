---
id: 202607281955
title: func.prototype vs __proto__ ([[js-prototype-chain-mechanics|Prototype]])
aliases:
  - func.prototype vs __proto__
  - constructor prototype vs instance prototype
  - prototype linkage js
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/objects
  - topic/prototypes
date_created: {today}
mastery_level: 1
---

# `func.prototype` vs `__proto__` (`[[js-prototype-chain-mechanics|Prototype]]`)

> **TL;DR:** `func.prototype` is the master blueprint stored on a factory function that will be stamped onto future children, whereas `__proto__` (`[[js-prototype-chain-mechanics|Prototype]]`) is the internal umbilical cord attached to an individual child object pointing back to the blueprint it was born from!

---

## Why This Exists
One of the most persistent points of confusion in JavaScript is distinguishing between a function's `.prototype` property and an object instance's internal `[[js-prototype-chain-mechanics|Prototype]]` link (accessible via `Object.getPrototypeOf(obj)` or `obj.__proto__`). Clarifying this distinction is essential for mastering object inheritance and V8 shape optimizations.

---

## Mental Model

```
       CONSTRUCTOR FUNCTION                          INSTANCE OBJECT
┌────────────────────────────────┐            ┌───────────────────────────┐
│ User(name)                     │            │ userInstance              │
│   prototype: 0x00A1 ───────────┼──┐         │   name: "Luffy"           │
└────────────────────────────────┘  │         │   [[js-prototype-chain-mechanics|Prototype]]: 0x00A1 ──┼──┐
                                    │         └───────────────────────────┘  │
                                    │                                        │
                                    ▼                                        ▼
                         ┌─────────────────────────────────────────────────────┐
                         │ PROTOTYPE OBJECT (Heap Address 0x00A1)              │
                         │   constructor: User                                 │
                         │   greet: function()                                 │
                         └─────────────────────────────────────────────────────┘
```

---

## How It Works

1. **`Function.prototype` Property:**
   - Only functions (excluding arrow functions) possess a public `.prototype` property.
   - It is an object automatically created whenever a function is declared.
   - It serves as the target template when the function is invoked with the `new` keyword.
2. **`Object [[js-prototype-chain-mechanics|Prototype]]` Internal Slot (`__proto__`):**
   - Every JavaScript object possesses an internal hidden slot named `[[js-prototype-chain-mechanics|Prototype]]`.
   - When executing `const user = new User("Luffy")`, V8 assigns `user.[[js-prototype-chain-mechanics|Prototype]] = User.prototype`.
   - Accessing `user.__proto__` invokes getter/setter methods on `Object.prototype.__proto__`.

---

## Comparison Table

| Attribute / Aspect | `Func.prototype` | `instance.__proto__` (`[[js-prototype-chain-mechanics|Prototype]]`) |
| :--- | :--- | :--- |
| **Where it exists** | On function objects only | On ALL JavaScript objects |
| **Role** | Blueprint for future `new` instances | Live link used during property resolution |
| **Access API** | Direct property `User.prototype` | `Object.getPrototypeOf(instance)` |
| **Arrow Functions?**| No (`undefined`) | Yes (Points to `Function.prototype`) |

---

## Canonical Code Example

```javascript
/**
 * Verifying func.prototype vs instance.[[js-prototype-chain-mechanics|Prototype]]
 */

function Hero(name) {
  this.name = name;
}

// Add method to Constructor prototype
Hero.prototype.attack = function() {
  return `${this.name} attacks!`;
};

const heroInstance = new Hero("Luffy");

// 1. Verify prototype equality link
console.log("Instance [[js-prototype-chain-mechanics|Prototype]] equals Hero.prototype:", 
  Object.getPrototypeOf(heroInstance) === Hero.prototype // Expected: true
);

// 2. Verify Constructor function's own [[js-prototype-chain-mechanics|Prototype]]
console.log("Hero function [[js-prototype-chain-mechanics|Prototype]] equals Function.prototype:",
  Object.getPrototypeOf(Hero) === Function.prototype // Expected: true
);

// 3. Arrow function prototype check
const arrowFunc = () => {};
console.log("Arrow Function .prototype:", arrowFunc.prototype); // Expected: undefined
```

---

## Key Takeaways
- `.prototype` is only used when a function acts as a constructor via `new`.
- `[[js-prototype-chain-mechanics|Prototype]]` (retrieved via `Object.getPrototypeOf()`) is the active chain link used for property lookups.
- `new Constructor()` wires `instance.[[js-prototype-chain-mechanics|Prototype]] = Constructor.prototype`.

---

## Related
- [[js-prototype-chain-mechanics]] — How property lookups walk up the prototype chain.
- [[js-constructor-functions]] — Constructor functions and `new` binding.
- [[js-es6-classes-basics]] — ES6 Class syntax and prototype linkage.
- [[MOC - JS Objects & Structures]] — Central Objects MOC.
