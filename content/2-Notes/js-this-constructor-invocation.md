---
id: 202607280831
title: this in Constructor & Class Invocations (new Binding)
aliases:
  - new binding
  - this in constructors
  - constructor this
  - class constructor this
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/this-keyword
  - topic/constructors
date_created: 2026-07-28
mastery_level: 1
---

# `this` in Constructor & Class Invocations (`new` Binding)

> **TL;DR:** Calling a function with `new` is like putting a fresh blank canvas on an easel. The `new` keyword creates the blank canvas, passes it into the painter function as `this`, lets the painter draw custom attributes onto `this`, and hands the painted canvas back to you automatically!

---

## Why This Exists
In object-oriented design, creating new object instances requires populating every fresh instance with its own distinct properties (e.g., `id`, `name`, `createdDate`). 

JavaScript solves instance initialization by introducing **`new` Binding** (Constructor Invocation). When a function or class constructor is invoked with the `new` operator, JavaScript overrides all other `this` binding rules, allocating a brand-new plain object in memory and binding `this` directly to that newly allocated instance for the duration of the constructor execution.

---

## Mental Model
Think of the `new` keyword as an automated manufacturing line:
1. Step 1: Allocates a raw unpainted vehicle frame `{}`.
2. Step 2: Installs the manufacturer badge (`[[js-prototype-chain-mechanics|Prototype]] = Constructor.prototype`).
3. Step 3: Binds `this` to the raw vehicle frame and runs `Constructor(this, args)`.
4. Step 4: Inside the constructor, `this.color = "red"` paints the frame.
5. Step 5: The completed vehicle (`this`) rolls off the line automatically.

```
INVOCATION: new User("Luffy")
                │
                ▼ (Engine Allocates Heap Memory)
[ Newly Created Instance {} ]
                │
                ▼ (Binds `this` to Instance)
`this.name = "Luffy"` ──► Mutates Instance ──► Returns Instance Automatically
```

---

## How It Works

When `new ConstructorFn(...args)` or `new ClassName(...args)` is executed:

1. **New Object Creation:** The engine creates a new, empty plain object in heap memory: `const instance = {}`.
2. **Prototype Link:** `instance.[[js-prototype-chain-mechanics|Prototype]]` is linked to `ConstructorFn.prototype`.
3. **`this` Binding:** The constructor is called with `this` bound to `instance`.
4. **Property Initialization:** Statements inside the constructor executing `this.prop = val` assign own properties directly to `instance`.
5. **Return Rule:**
   - If the constructor returns an explicit **Object** (non-primitive), that object replaces `this` as the return value.
   - If the constructor returns a primitive or nothing (`undefined`), `this` is returned automatically.

---

## Key Characteristics
- **Highest Priority Binding:** `new` binding overrides implicit dot binding (`obj.method()`) and explicit binding (`call()`, `apply()`, `bind()`).
- **Class Constructor Enforcement:** ES6 `class` constructors cannot be invoked without `new`. Attempting `UserClass()` without `new` throws a `TypeError: Class constructor cannot be invoked without 'new'`.

---

## Common Mistakes

### Mistake 1: Returning a custom object from a constructor unexpectedly
If a constructor function explicitly returns a custom object `{ foo: "bar" }`, the newly created `this` instance is discarded entirely!

```javascript
function BadUser(name) {
  this.name = name;
  return { override: true }; // DISCARDS 'this'!
}

const user = new BadUser("Luffy");
console.log(user.name);     // Output: undefined!
console.log(user.override); // Output: true
```

### Mistake 2: Using arrow functions as constructor functions
Arrow functions do not possess a ```[[js-ecmascript-internal-slots|[[Construct]]]]``` internal method and do not have their own `this`. Attempting `new ArrowFn()` throws a `TypeError: ArrowFn is not a constructor`.

---

## Best Practices
- **Never return objects explicitly** from constructor functions unless implementing a specific Factory or Singleton pattern.
- **Use ES6 `class` syntax** instead of ES5 constructor functions to get native runtime checks that enforce `new` usage.
- **Capitalize constructor functions** (`PascalCase`) to signal to developers that the function requires `new`.

---

## Comparison Table

| Invocation Context | `this` Value | Priority Level |
| :--- | :--- | :--- |
| **`new Constructor()`** | **Newly allocated instance `{}`** | **1 (Highest - Overrides all others)** |
| **`fn.bind(obj)()`** | Explicitly bound `obj` | 2 |
| **`obj.method()`** | Implicit owner `obj` | 3 |
| **`fn()` (Strict Mode)** | `undefined` | 4 (Lowest - Default) |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates 'new' keyword this binding, prototype linking,
 * overriding explicit bindings, and class constructor enforcement.
 */

// 1. ES5 Constructor Function
function Character(name, role) {
  // 'this' is bound to the fresh object created by 'new'
  this.name = name;
  this.role = role;
  this.created = new Date();
}

Character.prototype.getSummary = function() {
  return `${this.name} - ${this.role}`;
};

// Instantiating with 'new'
const luffy = new Character("Luffy", "Captain");
console.log("Instance Summary:", luffy.getSummary()); // Expected: "Luffy - Captain"
console.log("Instance Check:", luffy instanceof Character); // Expected: true

// 2. Proving 'new' Binding Priority Over Explicit 'bind()'
const externalContext = { name: "Fake Target", role: "Fake Role" };
const BoundCharacter = Character.bind(externalContext);

// 'new' overrides explicit 'bind(externalContext)'!
const zoro = new BoundCharacter("Zoro", "Swordsman");
console.log("Bound Override Check:", zoro.name); // Expected: "Zoro" (NOT "Fake Target"!)

// 3. ES6 Class Constructor Syntax (Enforces 'new')
class Enemy {
  constructor(name, power) {
    this.name = name;
    this.power = power;
  }
}

const kaido = new Enemy("Kaido", 9999);
console.log("Class Instance:", kaido.name); // Expected: "Kaido"

// Testing Enforcement Exception
try {
  // Attempting to invoke ES6 Class without 'new'
  Enemy("Kaido", 9999);
} catch (err) {
  console.log("Class Enforcement Error:", err.message);
  // Expected Output: Class constructor Enemy cannot be invoked without 'new'
}
```

---

## Key Takeaways
- Invoking a function or class with `new` allocates a fresh object and binds `this` to that instance.
- `new` binding has the highest priority in JavaScript, overriding implicit dot binding and explicit `.bind()`.
- Returning an explicit object from a constructor discards `this`; returning primitives ignores the return and delivers `this`.
- ES6 classes throw a runtime `TypeError` if invoked without `new`.

---

## Related
- [[js-constructor-functions]] — Constructor functions and prototype attachment.
- [[js-es6-classes-basics]] — Modern ES6 class fundamentals.
- [[js-explicit-binding-call-apply-bind]] — Comparing explicit binding vs `new` binding.
- [[MOC - JS Objects & Structures]] — Central Map of Content for Objects.
