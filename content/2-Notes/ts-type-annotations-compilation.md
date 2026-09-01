---
id: 202608152108
title: TypeScript Fundamentals, Type Annotations, and Compilation Workflow
aliases:
  - typescript fundamentals
  - type annotations
  - tsc compilation
  - ts-node
tags:
  - type/concept
  - status/processing
  - tool/typescript
  - lang/ts
  - topic/environment
date_created: 2026-08-15
mastery_level: 1
---

# TypeScript Fundamentals, Type Annotations, and Compilation Workflow

---

> **TL;DR:** TypeScript is a strongly-typed superset of JavaScript that adds explicit static type annotations (`string`, `number`, `boolean`, `any`) to catch bugs at compile-time via `tsc`, transpiling `.ts` files into standard JavaScript (`.js`).

---

## Why This Exists

JavaScript is dynamically typed, meaning a variable declared as a string (`let age = "20"`) can be reassigned to a number or array at runtime without warning—leading to runtime `TypeError` bugs in test frameworks. **TypeScript** adds static type checking and type annotations to JavaScript, flagging type mismatched assignments inside your IDE at compile-time before tests execute.

---

## Mental Model

Imagine building an electrical circuit.
- **JavaScript (Dynamic Typing):** Using generic un-coded wires. You can plug a high-voltage 220V appliance into a low-voltage 5V socket. The system doesn't stop you—until you flip the switch at runtime and blow up the machine.
- **TypeScript (Static Typing):** Using keyed, color-coded connectors (`: number`, `: string`). A 220V plug physically cannot fit into a 5V socket (`Type 'number' is not assignable to type 'string'`). The IDE catches the physical mismatch immediately while you assemble the circuit.

---

## How It Works

1. **Type Annotations Syntax:**  
   Append `: type` to variable declarations, function parameters, and return types:
   ```typescript
   let message: string = "Hello Playwright";
   let age: number = 30;
   let isActive: boolean = true;
   let scores: number[] = [90, 85, 95];
   ```
2. **Compile-Time Type Checking:**  
   If you attempt invalid reassignments:
   ```typescript
   let username: string = "Alice";
   username = 12345; // ❌ Compile Error: Type 'number' is not assignable to type 'string'.
   ```
3. **Compilation Workflow (`tsc`):**  
   Browsers and Node.js cannot execute `.ts` files directly. The TypeScript Compiler (`tsc`) strips type annotations and transpiles `.ts` files into runnable `.js` files:
   ```bash
   # Transpile TypeScript to JavaScript
   npx tsc script.ts

   # Run generated JavaScript file in Node
   node script.js
   ```

---

## Key Primitive Types Matrix

| Type Annotation | Example Value | Description |
| :--- | :--- | :--- |
| **`string`** | `"Playwright"`, `'Admin'` | Textual character data |
| **`number`** | `42`, `3.14`, `-10` | Integers and floating-point numbers |
| **`boolean`** | `true`, `false` | Logical truth values |
| **`number[]` / `Array<number>`** | `[1, 2, 3]` | Array of numbers |
| **`any`** | `"text"`, `10`, `true` | Disables strict type checking (behaves like JS) |

---

## Common Mistakes

- **Attempting to run `.ts` files directly in standard Node.js without transpilation:** Running `node test.ts`. Standard Node.js throws a syntax error on type annotations! Use `npx ts-node test.ts` or transpile first with `npx tsc test.ts`.
- **Overusing the `any` type:** Declaring all variables as `: any`. This disables TypeScript's safety features and converts your project back into plain JavaScript!

---

## Canonical TypeScript Example

```typescript
// TypeScript variable annotations, array types, and function signature
let userEmail: string = "anshika@gmail.com";
let totalItems: number = 5;
let isOrderComplete: boolean = true;
let productPrices: number[] = [299, 350, 499];

// Type annotations on function parameters and return type
function calculateTotalPrice(prices: number[], taxRate: number): number {
  const sum = prices.reduce((acc, curr) => acc + curr, 0);
  return sum * (1 + taxRate);
}

const finalTotal: number = calculateTotalPrice(productPrices, 0.08);
console.log(`User: ${userEmail}, Total: $${finalTotal.toFixed(2)}`);
```

---

## Key Takeaways

- TypeScript is a superset of JavaScript that adds compile-time type safety via annotations (`: string`, `: number`).
- Node.js cannot execute `.ts` files directly; use `tsc` to transpile `.ts` files to `.js`.
- Avoid overusing `: any`, as it disables static type safety.

---

## Related

- [[pw-js-vs-ts-migration]] — Migrating Playwright frameworks to TypeScript
- [[js-objects-vs-primitives]] — JS data types and primitives
- [[MOC - JS Variables & Execution]]
