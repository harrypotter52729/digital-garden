<%*
// 1. Prompt the user for input
const noteTitle = await tp.system.prompt("Enter Note Title", tp.file.title);
const noteType = await tp.system.prompt("Enter Note Type (e.g. concept, reference)");
const noteLang = await tp.system.prompt("Enter Language", "English");
const noteTopic = await tp.system.prompt("Enter Topic", "Storage");

// 2. Rename the file to match the title (Optional - remove if unwanted)
await tp.file.rename(noteTitle);
_%>
---
id: <% tp.file.creation_date("YYYYMMDDHHmm") %>
title: "<% noteTitle %>"
aliases: []
tags:
  - type/<% noteType %>
  - lang/<% noteLang.toLowerCase().replace(" ", "-") %>
  - status/processing
  - topic/<% noteTopic.toLowerCase() %>
date_created: <% tp.file.creation_date("YYYY-MM-DD") %>
date_modified: <% tp.file.last_modified_date("YYYY-MM-DD") %>
mastery_level: 1

---
## <% noteTitle %>


