---
id: 202606120826
title: Event Bubbling, Capturing & Delegation
aliases:
  - event-bubbling
  - event-delegation
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/dom
date_created: 2026-06-12
mastery_level: 1
---

# Event Bubbling, Capturing & Delegation

> **TL;DR:** **Bubbling** is like a rumor spreading upwards from a clerk to the CEO. **Event Delegation** is like a receptionist handling all mail for the entire office building, saving individual employees from having to wait by the door.

## The Mental Model
Imagine a nested folder layout: a document inside a folder, inside a filing cabinet.
- **Bubbling:** If you set fire to the document, the folder catches fire, and then the whole cabinet catches fire. The action travels *upward* to the parent containers.
- **Delegation:** Instead of hiring 10 security guards to watch 10 separate drawers in the filing cabinet, you hire *one* guard to watch the cabinet itself. When something happens to any drawer, the guard intercepts it and checks which drawer was touched.

---

## 1. The Propagation Phases
When you click an HTML element, the event travels in three distinct phases:

```
  1. Capturing (Trickles down)
       |         |
       v         |  3. Bubbling (Rises up)
   [window]      |       ^
       |         |       |
     [body]      |     [body]
       |         |       |
     [div]       v     [div]
       |     [target]    |
       +---> [button] ---+
             2. Target Phase
```

1. **Capturing Phase (Trickle Down):** The event starts at the absolute top (`window`) and travels down the [[js-dom-tree-and-element-selection|DOM tree]] search party style until it finds the clicked element.
2. **Target Phase:** The event arrives at the element you clicked (the `target`).
3. **Bubbling Phase (Rise Up):** The event floats straight back up the DOM tree from the target all the way back to the `window`. By default, almost all [[js-event-listeners-and-event-objects|event listeners]] fire during this **bubbling phase**.

---

## 2. Stopping the Spread: `stopPropagation()`
Sometimes you don't want an event to float up. For example, if you click a "Delete" button inside a clickable table row, you don't want the row's click event to also fire.
Calling `event.stopPropagation()` stops the event from rising any higher.

---

## 3. Event Delegation
Instead of adding [[js-event-listeners-and-event-objects|event listeners]] to 100 table cells or list items, you put a **single** listener on their parent container. Because events bubble up, any click on a child will bubble up to the parent anyway!

### Why use Delegation?
- **Performance:** Fewer event listeners mean less memory usage.
- **Dynamic Content:** If you add new items to the list later, they automatically have click detection without you having to attach new listeners.

---

## Canonical Code Example

Here is how to use event delegation to handle clicks on a list of buttons, and how to stop bubbling:

```javascript
// We select the PARENT container instead of individual items
const parentList = document.querySelector("#todo-list");

// Add a single listener to the parent
parentList.addEventListener("click", function (event) {
  // event.target is the actual element that was clicked
  const clickedElement = event.target;
  
  // 1. Check if the clicked element is a delete button
  if (clickedElement.classList.contains("delete-btn")) {
    console.log("Delete button clicked!");
    
    // Find the closest list item parent and remove it
    const listItem = clickedElement.closest("li");
    listItem.remove();
  }
});

// Stopping propagation example:
const outerDiv = document.querySelector(".outer-box");
const innerBtn = document.querySelector(".inner-btn");

outerDiv.addEventListener("click", () => {
  console.log("Outer box clicked!");
});

innerBtn.addEventListener("click", (event) => {
  console.log("Inner button clicked!");
  
  // 2. Prevent the click from bubbling up to outerDiv
  // Without this, you would see BOTH console logs.
  // With this, you only see "Inner button clicked!"
  event.stopPropagation();
});
```

---

## Related
* [[js-event-listeners-and-event-objects]] - Accessing target element properties safely.
* [[js-dom-tree-and-element-selection]] - Navigating parent-child DOM hierarchies.
* [[js-closures-and-lexical-environment]] - Capturing outer scopes within callbacks.
