<%*
// ------------------------------------------------------------------
// MASTER NOTE TEMPLATE
// Logic: Prompts user for Type and Language to enforce taxonomy.
// Source: Cognitive IDE Guide 
// ------------------------------------------------------------------

// 1. Prompt for Note Type (Enforces Taxonomy)

const noteType = await tp.system.suggester(
    ["Concept (Theory/Mental Model)", "Snippet (Code/How-To)", "Debugging Log (Error/Fix)", "Algorithm (CS Theory)", "MOC (Map of Content)"],
    ["concept", "snippet", "error", "algorithm", "moc"]
);

// 2. Prompt for Language/Domain (Populates Tags & Code Blocks)
// Defaulting to "text" if the user cancels the prompt to prevent errors.
const inputLang = await tp.system.prompt("Language or Topic (e.g., python, rust, system-design)");
const noteLang = inputLang || "text";

// 3. Auto-Rename "Untitled" Files (Reduces Friction)
let noteTitle = tp.file.title;
if (noteTitle.startsWith("Untitled") || noteTitle.length === 0) {
    noteTitle = await tp.system.prompt("Note Title");
    await tp.file.rename(noteTitle);

await tp.file.move("0-Inbox/" + noteTitle);
}
%>
---
id: <% tp.file.creation_date("YYYYMMDDHHmm") %>
title: "<%* tR += noteTitle %>"
aliases: []
tags:
  - type/<%* tR += noteType %>
  - lang/<%* tR += noteLang.toLowerCase().replace(" ", "-") %>
  - status/processing
date_created: <% tp.file.creation_date("YYYY-MM-DD") %>
date_modified: <% tp.file.last_modified_date("YYYY-MM-DD") %>
mastery_level: 1
source: ""
---

# <%* tR += noteTitle %>

<%* if (noteType === "concept") { %>
> **Feynman Definition:** [Explain this concept in your own words. Do not copy paste.]

## The Mental Model
- **Analogy:** [How does this map to something you already know?]
- **Why it exists:** [What problem does this solve?]

## Comparison / Differentials
| This Concept | vs | Alternative |
| :--- | :--: | :--- |
| ... | vs | ... |

## Implementation Syntax
```<%* tR += noteLang.toLowerCase() %>
// Canonical example demonstrating the concept
```

## Related Concepts
- [[ ]]
<%* } %>

<%* if (noteType === "snippet") { %>
> **Utility:** [What specific task does this solve? e.g., "Reverse a string"]

## The Code
```<%* tR += noteLang.toLowerCase() %>
// Paste runnable, self-contained code here
```

## Usage & Constraints
- **Input:** [Expected input format]
- **Output:** [Expected result]
- **Warning:** [Edge cases where this fails, e.g., O(n^2) on large lists]

## Source
- https://orgmode.org/guide/Hyperlinks.html
<%* } %>

<%* if (noteType === "error") { %>
## The Error
> **Message:** `[Paste exact error message or stack trace header here for searchability]`

## Context
- **Scenario:** [What were you doing when it crashed?]
- **Environment:** [e.g., Production, Localhost, venv]

## The Fix
1. [Step 1]
2. [Step 2]

## Root Cause Analysis
- **Why did it happen?** [e.g., "Timeout was too short for batch size"]
- **How to prevent?** [e.g., "Added a liveness probe config"]
<%* } %>

<%* if (noteType === "algorithm") { %>
## Theory
- **Definition:** [Formal definition]
- **Complexity:** Time: $O( )$ | Space: $O( )$

## Visual Model
![[<%* tR += noteTitle %>-diagram.excalidraw]]
*[Use Excalidraw to sketch the data flow]*

## Implementation
```<%* tR += noteLang.toLowerCase() %>
// Class or Function implementation
```

## Edge Cases
- **Worst Case:** [When does performance degrade?]
<%* } %>

<%* if (noteType === "moc") { %>
## Learning Path: <%* tR += noteLang %>
**Goal:** Master <%* tR += noteLang %>

### 1. Syntax & Fundamentals
- [[ ]]

### 2. Core Concepts (Semantics)
- [[ ]]

### 3. Advanced & Architecture
- [[ ]]
<%* } %>

---
**References:**
-