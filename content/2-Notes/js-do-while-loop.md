---
id: 202602080849
title: js-do-while-loop
aliases: []
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/
date_created: 2026-02-08
date_modified: 2026-02-08
mastery_level: 1
---
## js-do-while-loop


## Concept
A variation of the while loop where the condition is evaluated *after* the code block executes.

## Key Distinction
* **While Loop:** May execute **zero** times if the condition is false initially.
* **Do-While Loop:** Guaranteed to execute **at least once**.

## Syntax
```javascript
let yourName;
do {
  yourName = prompt("Who are you?");
}while (!yourName);
```
In this example, the prompt must appear at least once to get the data required for the check.