---
id: 202607280816
title: Inline Caches (Monomorphic, Polymorphic, Megamorphic)
aliases:
  - inline caches
  - ICs
  - monomorphic IC
  - polymorphic IC
  - megamorphic IC
  - V8 inline caching
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/v8-internals
  - topic/performance
date_created: 2026-07-28
mastery_level: 1
---

# Inline Caches (Monomorphic, Polymorphic, Megamorphic)

> **TL;DR:** Imagine a fast-food cashier. If every single customer orders a Cheeseburger (Monomorphic), the cashier types the order instantly without thinking. If customers alternate between 2 or 3 items (Polymorphic), the cashier checks a short cheat sheet. But if every customer orders something completely unique from a secret menu (Megamorphic), the cashier gives up, pulls out a 200-page manual, and reads every line slowly!

---

## Why This Exists
Even with V8 Hidden Classes mapping property names to memory offsets, performing a shape check and offset lookup on every property access (`obj.x`) still requires multiple CPU instructions per read.

In production applications, hot loops often access properties on objects that have the exact same shape over and over again. **Inline Caching (IC)** is V8's core optimization mechanism that patch machine code at call sites at runtime, replacing property lookup calls directly with hardcoded assembly memory offsets.

---

## Mental Model
Think of an Inline Cache as a fast-path shortcut stamped directly into compiled machine code at a specific line of code:
- **Line 42: `return user.name;`**
- First Run (Uninitialized): The engine looks up `name` in `user`'s Hidden Class `C1`, finds offset 0, and updates the IC stub on Line 42: *"If object has Shape `C1`, jump straight to Offset 0!"*
- Subsequent Runs (Monomorphic): Next time Line 42 runs, V8 verifies `user.shape === C1` and reads `Offset 0` in a single CPU instruction instruction.

```
PROPERTY ACCESS CALL SITE: obj.name
                │
                ▼
        [ Check IC State ]
                │
 ┌──────────────┼──────────────────────────────┐
 ▼              ▼                              ▼
MONOMORPHIC     POLYMORPHIC                    MEGAMORPHIC
(1 Shape Seen)  (2 to 4 Shapes Seen)           (5+ Shapes Seen)
 │               │                              │
 ▼               ▼                              ▼
Fastest Offset   Short Switch Table / Stub      Slow Hash/Runtime Lookup
(1 Instruction)  (2-4 Checks)                   (Deoptimized Fallback)
```

---

## How It Works

V8 transitions an Inline Cache through four distinct states based on the diversity of object shapes observed at a specific call site:

1. **Uninitialized State:** The call site hasn't executed yet.
2. **Monomorphic State (FASTEST):**
   - The call site has seen **exactly one** Hidden Class shape.
   - V8 patches machine code directly to read the hardcoded memory offset if shape matches.
   - Access speed: Equal to C++ struct field access!
3. **Polymorphic State (FAST):**
   - The call site has seen **2 to 4 distinct shapes** (e.g., passing `Dog` or `Cat` objects to `animal.speak()`).
   - V8 updates the IC stub to a small inline conditional switch table checking shape IDs against 2-4 known offsets.
4. **Megamorphic State (SLOWEST):**
   - The call site has seen **5 or more different shapes**.
   - V8 abandons inline machine code patching entirely. The IC transitions to a global stub lookup or dynamic dictionary hash search.

---

## Key Characteristics
- **Call-Site Specific:** IC states are tracked per call site in code, not per object. Line 10 might be Monomorphic while Line 50 is Megamorphic.
- **Irreversible Degradation:** Once a call site degrades from Monomorphic to Polymorphic or Megamorphic, V8 rarely transitions it back to Monomorphic without recompilation.
- **Ignition & TurboFan Integration:** Monomorphic ICs feed profiling feedback directly into TurboFan (V8's optimizing compiler) to generate speculative machine code.

---

## Common Mistakes

### Mistake 1: Passing objects of wildly varying shapes into hot utility functions
Passing dozens of different object shapes into a single formatting function degrades its IC from Monomorphic to Megamorphic.

```javascript
// HOT UTILITY FUNCTION:
function getName(entity) {
  return entity.name; // CALL SITE
}

// BAD: Called with 10 different object layouts throughout the app!
getName({ name: "A", a: 1 });
getName({ name: "B", b: 2 });
getName({ name: "C", c: 3 });
getName({ name: "D", d: 4 });
getName({ name: "E", e: 5 }); // Call site becomes MEGAMORPHIC!
```

### Mistake 2: Assuming all array methods preserve IC monomorphism
Passing sparse arrays, arguments objects, or mixed-type arrays into high-frequency loops forces array iteration ICs into slow megamorphic fallback paths.

---

## Best Practices
- **Keep functions monomorphic** by passing objects with identical shapes to high-frequency utility functions and hot loops.
- **Group polymorphic function calls** into separate dedicated functions if handling distinct types.
- **Use TypeScript interfaces or class constructors** to enforce consistent object shapes across the application codebase.

---

## Comparison Table

| IC State | Shapes Observed | Internal Mechanism | Lookup Speed | TurboFan Optimization |
| :--- | :--- | :--- | :--- | :--- |
| **Uninitialized** | 0 Shapes | Triggers initial shape lookup | N/A | None |
| **Monomorphic** | **1 Shape** | Hardcoded Assembly Memory Offset | **Fastest (~1 CPU cycle)** | **Optimized JIT Speculation** |
| **Polymorphic** | **2 – 4 Shapes** | Small shape-check switch table | Fast (~2-5 CPU cycles) | Partially Speculated |
| **Megamorphic** | **5+ Shapes** | Global Stub Cache / Hash Table | **Slowest (Fallback)** | Deoptimized / Bypassed |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates Inline Cache states (Monomorphic vs Polymorphic vs Megamorphic)
 * and benchmarks the real-world performance impact of IC state degradation.
 */

// 1. Defining Classes with Stable Shapes
class Car { constructor(make) { this.make = make; } }
class Truck { constructor(make) { this.make = make; } }
class Bike { constructor(make) { this.make = make; } }
class Boat { constructor(make) { this.make = make; } }
class Plane { constructor(make) { this.make = make; } }

// 2. Monomorphic Function Call Site
function getCarMake(vehicle) {
  return vehicle.make; // IC CALL SITE 1
}

// 3. Polymorphic / Megamorphic Function Call Site
function getGenericMake(vehicle) {
  return vehicle.make; // IC CALL SITE 2
}

const carInstance = new Car("Toyota");
const truckInstance = new Truck("Ford");
const bikeInstance = new Bike("Yamaha");
const boatInstance = new Boat("SeaRay");
const planeInstance = new Plane("Boeing");

// Warming up Monomorphic Call Site (Always receives Car shape)
for (let i = 0; i < 1000; i++) {
  getCarMake(carInstance); // Call site 1 becomes MONOMORPHIC!
}

// Degrading Generic Call Site to Megamorphic (Receives 5+ shapes)
const vehicles = [carInstance, truckInstance, bikeInstance, boatInstance, planeInstance];
for (let i = 0; i < 1000; i++) {
  getGenericMake(vehicles[i % 5]); // Call site 2 transitions MONO -> POLY -> MEGAMORPHIC!
}

// 4. Benchmarking Monomorphic vs Megamorphic Execution Speed
const ITERATIONS = 20_000_000;

console.time("Monomorphic IC Loop");
let val1 = "";
for (let i = 0; i < ITERATIONS; i++) {
  val1 = getCarMake(carInstance);
}
console.timeEnd("Monomorphic IC Loop");

console.time("Megamorphic IC Loop");
let val2 = "";
for (let i = 0; i < ITERATIONS; i++) {
  val2 = getGenericMake(vehicles[i % 5]);
}
console.timeEnd("Megamorphic IC Loop");
```

---

## Key Takeaways
- Inline Caching (IC) accelerates property lookups by patching machine code at call sites with direct assembly memory offsets.
- **Monomorphic ICs** (seeing 1 shape) are the fastest, enabling TurboFan to generate optimized JIT assembly.
- **Polymorphic ICs** (seeing 2-4 shapes) use a fast shape-check stub table.
- **Megamorphic ICs** (seeing 5+ shapes) fall back to slower global stub lookups and dynamic hash searches.
- Ensure hot loops and high-frequency utility functions receive consistent object shapes to preserve monomorphism.

---

## Related
- [[js-v8-hidden-classes-shapes]] — How V8 creates and transitions object shapes.
- [[js-v8-fast-properties-vs-dictionary-mode]] — Storage layouts for fast in-object properties vs dictionary mode.
- [[js-v8-deoptimization-pitfalls]] — Common code anti-patterns that trigger TurboFan deoptimizations.
- [[MOC - JS Advanced Mechanics & Metaprogramming]] — Central Map of Content for Engine Internals.
