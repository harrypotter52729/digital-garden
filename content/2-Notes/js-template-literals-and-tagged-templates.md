---
id: 202606120830
title: Template Literals & Tagged Templates
aliases:
  - template-literals
  - tagged-templates
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/syntax
date_created: 2026-06-12
mastery_level: 1
---

# Template Literals & Tagged Templates

> **TL;DR:** **Template Literals** are like **Mad Libs** templates (filling in blanks inside backticks). **Tagged Templates** are like a **censor pen**—they let you pass the template literal through a function to clean up or transform the words *before* they are displayed.

## 1. Template Literals (String Interpolation)
Before ES6, combining strings and variables required [[js-operator-overloading-and-string-dominance|additions (`+`)]] and quote escapes. Template literals use backticks (`` ` ``) and placeholders (`${expression}`) to make formatting straightforward.

- **Dynamic Evaluation:** You can put any JavaScript expression inside `${}`—variables, math, or function calls.
- **Multiline Support:** Standard newlines inside backticks translate directly into the output string without requiring `\n`.

```javascript
const name = "Hemanth";
const note = `Hello ${name},
this is a multiline
string!`;
```

---

## 2. Tagged Templates (Advanced Hooking)
A tagged template is a more advanced syntax where you prefix a template literal with a [[js-higher-order-functions-and-callbacks|function name]] (the "tag").

```javascript
myTag`Hello ${name}, you are ${age} years old.`;
```

This does **not** instantly evaluate the string. Instead, it calls `myTag` and passes:
1. An array of the static string parts (e.g., `["Hello ", ", you are ", " years old."]`).
2. The evaluated placeholder arguments (e.g., `name` and `age` variables).

### Why use Tagged Templates?
- **Sanitizing HTML:** Escaping user input to block XSS attacks.
- **CSS-in-JS:** Writing styled components (e.g., `styled.div` in React).
- **Internationalization (i18n):** Automatically translating string pieces.

---

## Canonical Code Example

Here is a script showing basic template literals and a custom tagging function that censors bad words:

```javascript
// --- 1. Basic Template Literal ---
const product = "Laptop";
const price = 999;
const tax = 0.08;

const invoice = `Item: ${product}
Price: $${price}
Total (with tax): $${(price * (1 + tax)).toFixed(2)}`;
console.log(invoice);


// --- 2. Tagged Template (Censor Function) ---
function censor(strings, ...values) {
  // Censor word list
  const bannedWords = ["badword", "javascript-sucks"];
  
  // Reconstruct the string, cleaning up the variables
  return strings.reduce((acc, str, index) => {
    let value = values[index - 1];
    
    // If the inserted variable is a string, check it for banned words
    if (typeof value === "string") {
      bannedWords.forEach(word => {
        if (value.toLowerCase().includes(word)) {
          value = "🚨 [CENSORED] 🚨";
        }
      });
    }
    
    return acc + value + str;
  });
}

const opinion = "JavaScript-sucks";
const userReview = censor`I think that ${opinion} is the truth!`;
console.log(userReview); 
// Output: "I think that 🚨 [CENSORED] 🚨 is the truth!"
```

---

## Related
* [[MOC - JS Data Types & Memory]] - String memory management.
* [[js-higher-order-functions-and-callbacks]] - Tag functions as higher-order processors.
* [[js-operator-overloading-and-string-dominance]] - Legacy string concatenation additions.
