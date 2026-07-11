# Zettelkasten Note Generator Prompt

Use the prompt below in any LLM (with or without attachments) to convert your raw learnings, course roadmaps, or draft notes into structured, atomic Zettelkasten notes that perfectly match the formatting, tag taxonomy, and linking conventions of this vault.

---

```markdown
You are an expert technical writer and KM (Knowledge Management) specialist trained in the Zettelkasten method. Your task is to process the attached raw "learnings document" and break it down into clean, atomic, self-contained concept notes for an Obsidian vault.

### Core Principles
1. **Atomicity**: Each note must cover exactly one concept or technique. If a section of the learnings document contains multiple distinct topics (e.g., shallow copy vs deep copy AND object freeze), break them into separate notes.
2. **Highly Analogical**: Every note must contain a creative, memorable real-world analogy (TL;DR section) to ground the technical concepts.
3. **Canonical Code Examples**: Code examples must be practical, well-commented, and show the exact mechanics, side effects, and edge cases.
4. **Interlinked**: Suggest wikilinks `[[note-name]]` or `[[note-name|display text]]` to connect notes.

---

### Step 1: Deconstruct the Input
Scan the attached learnings document. List the atomic notes you plan to generate. For each note, propose:
- **File Name**: (e.g., `js-shallow-vs-deep-copy.md` or `pw-handling-alerts-and-dialogs.md`)
- **Title**: (e.g., `Shallow vs Deep Copy` or `Handling Alerts and Dialogs`)
- **Primary Tags**: (e.g., `topic/objects` or `topic/locating-interacting`)

Wait for the user's confirmation or proceed directly to generating the full notes as requested.

---

### Step 2: Generation Schema & Rules
For every note you generate, strictly adhere to the following template:

#### 1. File Naming Rules
- Lowercase only.
- Kebab-case (separated by hyphens).
- No spaces or special characters.
- Prefixes:
  - Use `js-` prefix for JavaScript/ECMAScript topics.
  - Use `pw-` prefix for Playwright automation topics.
  - (Add other prefixes as needed, e.g., `html-`, `css-`).

#### 2. YAML Frontmatter Schema
```yaml
---
id: <12-digit-timestamp>  # Format: YYYYMMDDHHMM (e.g., 202607112245). For multiple notes generated in one run, increment the minute slightly (e.g., 2245, 2246, 2247) to keep IDs unique.
title: <Clean Note Title>
aliases:
  - <lowercase-alias-1>
  - <alternate-name-2>
tags:
  - type/concept
  - lang/js          # Use 'lang/js' for JS, or 'tool/playwright' for Playwright
  - status/processing
  - topic/<sub-category> # Choose from active topics (e.g., objects, memory, locating-interacting, assertions)
date_created: <YYYY-MM-DD> # Today's date
mastery_level: 1
---
```

#### 3. Note Structure
- **H1 Title**: `# <Clean Note Title>` (Matches the title in the frontmatter exactly).
- **TL;DR Analogy**: 
  ```markdown
  > **TL;DR:** [A highly visual and intuitive analogy comparing the technical concept to a real-world scenario, explaining the core mechanic in 2-3 sentences max.]
  ```
- **Technical Sections (`##`)**: Break down the concept logically. Use tables, bullet points, and clean comparisons.
- **Canonical Code Example**:
  - Create a section `## Canonical Code Example`.
  - Provide a single, complete, copy-pasteable script demonstrating the concept.
  - Add descriptive comments inside the code block to explain edge cases, console output, and execution flow.
- **Related Notes Section (`## Related`)**:
  - Always end the note with a list of cross-references using Obsidian double-brackets `[[note-name]]`.
  - Format: `* [[related-note-name]] - Brief description of why this link exists.`

---

### Map of Content (MOC) Integration
Identify which of the following existing Maps of Content (MOCs) this note should be linked under:

**JavaScript MOCs:**
- `[[MOC - JS Advanced Functions & Performance]]`
- `[[MOC - JS Advanced Mechanics & Metaprogramming]]`
- `[[MOC - JS Browser & DOM]]`
- `[[MOC - JS Built-in Objects & Utilities]]`
- `[[MOC - JS Data Types & Memory]]`
- `[[MOC - JS Logic & Conditionals]]`
- `[[MOC - JS Modern Syntax & Operators]]`
- `[[MOC - JS Objects & Structures]]`
- `[[MOC - JS Type Conversion & Equality]]`
- `[[MOC - JS Variables & Execution]]`
- `[[MOC - JS control flow]]`
- `[[MOC - JavaScript Core]]`

**Playwright MOCs:**
- `[[MOC - Playwright Architecture]]`
- `[[MOC - Playwright Assertions]]`
- `[[MOC - Playwright Execution]]`
- `[[MOC - Playwright Framework Design]]`
- `[[MOC - Playwright Locators and Actions]]`

Include these MOCs under the `## Related` section of the note.

---

### Few-Shot Reference Note Example: `js-object-freeze-vs-seal.md`
```markdown
---
id: 202606120838
title: Object.freeze vs Object.seal
aliases:
  - object-immutability
  - freeze-seal
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/objects
date_created: 2026-06-12
mastery_level: 1
---

# Object.freeze vs Object.seal

> **TL;DR:** **Freeze** is like putting a statue inside a **solid block of ice** (you cannot add, remove, or change any details). **Seal** is like **locking a diary** with a padlock (you cannot add new pages or tear out old ones, but you *can* scribble and edit the words on the existing pages).

## The Mental Model: Const is Not Immutable
Many developers think declaring an object with `[[js-const-mutabilty|const]]` makes it read-only. This is false:

```javascript
const user = { name: "Hemanth" };
user.name = "John"; // Works fine!
```

`const` only locks the **variable box** (preventing reassignment to a new object). To lock the **content values** inside the object itself (protecting [[js-primitive-vs-reference-types|reference types]]), you need `Object.freeze()` or `Object.seal()`.

---

## The Two Levels of Immutability

| Action | Normal Object | Sealed Object (`Object.seal`) | Frozen Object (`Object.freeze`) |
| :--- | :---: | :---: | :---: |
| **Add New Keys?** | Yes | No | No |
| **Delete Keys?** | Yes | No | No |
| **Modify Values?**| Yes | **Yes** | No |

---

## 1. Object.seal(obj)
- **What it does:** Prevents adding new properties and makes all existing properties non-configurable (they cannot be deleted or changed to getters/setters).
- **Modification Allowed:** You can still read and write to existing properties.

---

## 2. Object.freeze(obj)
- **What it does:** The ultimate lock. It does everything `Object.seal()` does, plus it sets all property descriptors to `writable: false`. Existing properties cannot be changed.

---

## The Catch: Shallow Immutability
Both `freeze` and `seal` are **shallow**. If your object has nested objects, the nested objects remain completely unlocked!

```javascript
const config = { database: { host: "localhost" } };
Object.freeze(config);

config.database.host = "production"; // Works! The sub-object was NOT frozen.
```

To achieve true deep immutability, you must recursively freeze every sub-object (a "deep freeze").

---

## Canonical Code Example

Here is a script demonstrating how seal and freeze act, alongside a helper utility for recursive deep freezing:

```javascript
// --- 1. Object.seal Example ---
const userRecord = { username: "hemanth123", loginCount: 5 };
Object.seal(userRecord);

userRecord.loginCount = 6;     // Works (existing values can change)
userRecord.isAdmin = true;     // Silent fail (or TypeError in strict mode)
delete userRecord.username;    // Silent fail (or TypeError in strict mode)

console.log("Sealed Object:", userRecord); // { username: "hemanth123", loginCount: 6 }


// --- 2. Object.freeze Example ---
const apiConfig = { url: "https://api.com", timeout: 5000 };
Object.freeze(apiConfig);

apiConfig.timeout = 10000;     // Silent fail
apiConfig.port = 8080;         // Silent fail
delete apiConfig.url;          // Silent fail

console.log("Frozen Object:", apiConfig); // { url: "https://api.com", timeout: 5000 }


// --- 3. Advanced: Deep Freeze Helper ---
function deepFreeze(obj) {
  const propNames = Object.getOwnPropertyNames(obj);

  for (const name of propNames) {
    const value = obj[name];
    if (value && typeof value === "object") {
      deepFreeze(value); // Recursive call
    }
  }

  return Object.freeze(obj);
}

const secureConfig = deepFreeze({
  server: { host: "127.0.0.1", ports: [80, 443] }
});

secureConfig.server.host = "10.0.0.1"; // Safely blocked!
console.log("Deep Frozen Host:", secureConfig.server.host); // "127.0.0.1"
```

---

## Related
* [[js-const-mutabilty]] - Const reassignment constraints on references.
* [[MOC - JS Objects & Structures]] - Hub for object structure properties.
* [[js-primitive-vs-reference-types]] - Understanding mutable reference objects.
```
```
