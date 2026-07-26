---
id: 202607261333
title: "Typed Arrays and Raw Binary Memory: ArrayBuffer, TypedArray, and DataView"
aliases:
  - typed-arrays
  - arraybuffer-and-dataview
  - binary-data-javascript
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/arrays
  - topic/memory
  - topic/binary
date_created: 2026-07-26
mastery_level: 1
---

# Typed Arrays and Raw Binary Memory: ArrayBuffer, TypedArray, and DataView

> **TL;DR:** Standard JavaScript arrays store dynamic mixed types with high overhead. **`ArrayBuffer`** allocates a fixed-length block of raw binary memory. **`TypedArray`** views (e.g., `Uint8Array`, `Float64Array`) provide fast, typed, contiguous numeric access ideal for WebGL, canvas image processing, audio/video streaming, and binary network protocols.

## Standard Arrays vs. Typed Arrays

Standard JS arrays are dynamically resized objects capable of holding strings, objects, numbers, and functions. This flexibility introduces memory fragmentation and pointer overhead. 

**Typed Arrays** enforce fixed-length, monomorphic numeric types stored in contiguous memory buffers:

| Feature | Standard Array (`[]`) | Typed Array (e.g., `Int32Array`) |
| :--- | :--- | :--- |
| **Allowed Elements** | Any JS type (mixed) | Fixed numeric types only |
| **Resizing (`length`)**| Dynamic (Can grow/shrink) | **Fixed** at creation time |
| **Memory Allocation** | Heap object dictionary | Contiguous binary buffer |
| **Performance** | General-purpose | High-speed (Graphics/WebGL/WebAssembly) |
| **Can Contain Holes?**| Yes (Sparse) | **No** (Defaults unassigned slots to `0`) |

---

## 1. The Three Layers of Binary Data

1. **`ArrayBuffer`**: Represents a raw, opaque block of binary memory bytes. Cannot be read or modified directly.
2. **`TypedArray`**: A typed view over an `ArrayBuffer` where every element has the same numeric byte size (e.g., 8-bit, 16-bit, 32-bit integer or float).
3. **`DataView`**: A low-level, flexible view over an `ArrayBuffer` allowing heterogeneous byte reading/writing with explicit endianness control (`littleEndian`).

---

## Common Typed Array Variants

| Typed Array Class | Byte Size | Range per Element | Common Use Cases |
| :--- | :---: | :--- | :--- |
| **`Int8Array`** | 1 | $-128 \text{ to } 127$ | Signed 8-bit integer processing |
| **`Uint8Array`** | 1 | $0 \text{ to } 255$ | File streams, Canvas `ImageData`, UTF-8 bytes |
| **`Uint8ClampedArray`**| 1 | $0 \text{ to } 255$ (Clamps out-of-range values) | Canvas RGBA pixel manipulation |
| **`Int16Array`** | 2 | $-32,768 \text{ to } 32,767$ | Audio processing |
| **`Int32Array`** | 4 | $-2,147,483,648 \text{ to } 2,147,483,647$ | 32-bit math, WebGL index buffers |
| **`Float32Array`** | 4 | 32-bit IEEE 754 Floating Point | WebGL vertex buffers, 3D graphics |
| **`Float64Array`** | 8 | 64-bit IEEE 754 Floating Point | High-precision scientific calculations |

---

## Canonical Code Example

This script demonstrates raw memory allocation with `ArrayBuffer`, typed views with `Uint8Array`, clamping behavior, and `DataView` endianness manipulation:

```javascript
// --- 1. Allocating Raw Memory (ArrayBuffer) ---
// Allocate 8 bytes of raw unformatted memory
const rawBuffer = new ArrayBuffer(8);
console.log("Buffer Byte Length:", rawBuffer.byteLength); // 8

// --- 2. Creating a TypedArray View over the Buffer ---
// Interpret the 8-byte buffer as 8 unsigned 8-bit integers
const uint8View = new Uint8Array(rawBuffer);
uint8View[0] = 255;
uint8View[1] = 100;

console.log("Uint8 View Elements:", uint8View); // [255, 100, 0, 0, 0, 0, 0, 0]

// --- 3. Clamping Behavior (Uint8ClampedArray for Canvas Pixels) ---
const clampedView = new Uint8ClampedArray(2);
clampedView[0] = 300;  // Overflow clamped to 255
clampedView[1] = -50;  // Underflow clamped to 0
console.log("Clamped View Output:", clampedView); // [255, 0]

// --- 4. Heterogeneous Reading/Writing via DataView ---
const viewBuffer = new ArrayBuffer(4); // 4 bytes
const dataView = new DataView(viewBuffer);

// Store a 16-bit integer at byte offset 0 (Big-Endian)
dataView.setInt16(0, 500, false); 

// Read back as two separate 8-bit unsigned integers:
console.log("Byte 0:", dataView.getUint8(0)); // 1 (High byte: 500 >> 8)
console.log("Byte 1:", dataView.getUint8(1)); // 244 (Low byte: 500 & 0xFF)

// --- 5. High Performance Subarrays & Methods ---
const numbers = new Float32Array([1.5, 2.5, 3.5]);
const doubled = numbers.map(x => x * 2); // Typed arrays support standard array iteration methods!
console.log("Mapped Float32Array:", doubled); // Float32Array [3, 5, 7]
```

---

## Related
* [[js-array-creation-methods]] - Array creation techniques.
* [[js-array-length-mechanics]] - Fixed length constraints on typed arrays.
* [[js-array-specialized-objects]] - Objects vs contiguous memory structures.
* [[MOC - JS Data Types & Memory]] - Binary data handling in JavaScript.
* [[MOC - JS Browser & DOM]] - Canvas ImageData and WebGL APIs.
