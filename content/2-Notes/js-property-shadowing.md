---
id: 202607281958
title: Property Shadowing on Prototype Chain
aliases:
  - property shadowing
  - prototype shadowing
  - shadowing js
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/objects
  - topic/prototypes
date_created: {today}
mastery_level: 1
---

# Property Shadowing on Prototype Chain

> **TL;DR:** Property Shadowing occurs when an own property on a child object has the exact same name as an inherited property on its prototype—effectively masking the prototype property during lookups like a tree branch blocking out sunlight!

---

## Canonical Code Example

```javascript
/**
 * Demonstrates Property Shadowing and prototype unmasking via delete.
 */

const vehicleProto = {
  wheels: 4,
  describe() { return `Vehicle with ${this.wheels} wheels.`; }
};

const car = Object.create(vehicleProto);

// 1. Inherited lookup
console.log("Initial wheels:", car.wheels); // Expected: 4 (From vehicleProto)

// 2. Own property assignment shadows prototype property
car.wheels = 3; // Custom three-wheeler
console.log("Shadowed wheels:", car.wheels); // Expected: 3 (Own property)
console.log("Proto wheels remains:", vehicleProto.wheels); // Expected: 4

// 3. Deleting own property unmasks prototype property
delete car.wheels;
console.log("Unmasked wheels after delete:", car.wheels); // Expected: 4
```

---

## Related
- [[js-prototype-chain-mechanics]] — Mechanics of ```[[js-ecmascript-internal-slots|[[Get]]]]``` prototype traversal.
- [[js-object-property-existence-checks]] — `Object.hasOwn()` vs prototype lookups.
- [[MOC - JS Objects & Structures]] — Central Objects MOC.
