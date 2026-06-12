---
id: 202602081402
title: Call, Apply, and Bind Methods
aliases: [call, apply, bind, Explicit Binding]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/oop
date_created: {{date}}
mastery_level: 1
---
# Call, Apply, and Bind Methods

Because functions are first-class objects in JavaScript, they have their own built-in methods. `call`, `apply`, and `bind` are methods on `Function.prototype` used to explicitly define what the `this` keyword should point to when a function executes.

## 1. `call(thisArg, arg1, arg2...)`
Executes the function immediately. You pass the object you want `this` to point to as the first argument, followed by the function's standard arguments separated by commas.
```javascript
function introduce(lang1, lang2) {
  console.log(`I am ${this.name} and I write ${lang1} and ${lang2}.`);
}
const dev = { name: "Alice" };

introduce.call(dev, "JavaScript", "Python"); 
// "I am Alice and I write JavaScript and Python."
```

## 2. `apply(thisArg, [argsArray])`

Identical to `call`, but it takes the function's arguments as an **Array** instead of comma-separated values. _(Mnemonic: **A**pply takes an **A**rray)._



```JavaScript
introduce.apply(dev, ["JavaScript", "Python"]);
```

## 3. `bind(thisArg, arg1, arg2...)`

Unlike `call` and `apply`, `bind` **does not execute the function immediately**. Instead, it returns a _brand new function_ with the `this` keyword permanently locked to the provided object.



```JavaScript
const boundIntroduce = introduce.bind(dev, "JavaScript", "Python");
// Later in the code...
boundIntroduce(); // Executes safely with 'this' locked to 'dev'
```

_Note: `bind` is heavily used in React (class components) and event listeners to ensure the context isn't lost when the callback executes._

## Related

- [[js-the-this-keyword-rules]] - The context for why explicit binding is needed.
    
- [[js-callbacks-and-inversion-of-control]] - Callbacks frequently lose their `this` context, requiring `.bind()`.