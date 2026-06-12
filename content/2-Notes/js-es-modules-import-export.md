---
id: 202602081503
title: ES Modules (Import and Export)
aliases: [ESM, CommonJS, Modules]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/architecture
date_created: {{date}}
mastery_level: 1
---
# ES Modules (Import and Export)

As JavaScript applications grew, keeping all code in one file became impossible. Modules allow us to split code into separate, isolated files and share specific pieces of logic between them.

## The Modern Standard: ES Modules (ESM)
Introduced in ES6, this is the official module system for JavaScript (used natively in browsers and modern Node.js).

### 1. Named Exports
Used when you want to export multiple variables/functions from a single file. You must import them using the exact same name, wrapped in curly braces.

```javascript
// mathUtils.js
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;

// app.js
import { add, subtract } from './mathUtils.js';
```

### 2. Default Exports

Used when a file has one primary purpose (like exporting a single Class or React Component). You can name the import whatever you want, and it doesn't use curly braces.

JavaScript

```
// User.js
export default class User { /* ... */ }

// app.js
import AnyNameIWant from './User.js';
```

## The Legacy System: CommonJS (Node.js)

Before ES Modules existed, Node.js created its own module system called CommonJS. You will still see this heavily in older codebases and backend environments.

Instead of `import`/`export`, CommonJS uses `require()` and `module.exports`.



```JavaScript
// CommonJS Export (legacyNodeFile.js)
module.exports = function doSomething() { /* ... */ };

// CommonJS Import
const doSomething = require('./legacyNodeFile.js');
```

## Key Difference in Execution

- **ES Modules (import):** Are statically analyzed. The JS engine reads all imports at the top of the file _before_ executing the code. This allows for dead-code elimination (Tree Shaking).
    
- **CommonJS (require):** Are dynamically evaluated at runtime. You can put a `require()` statement inside an `if` block, which you cannot do with standard `import`.
    

## Related

- [[js-iife-immediately-invoked-function-expression]] - The hacky way we split code into files before modules existed.