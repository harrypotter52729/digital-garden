---
id: 202602081401
title: The 'this' Keyword Rules
aliases: [this keyword, this binding]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/oop
date_created: {{date}}
mastery_level: 1
---
# The `this` Keyword Rules

In JavaScript, the `this` keyword evaluates to the object that is currently executing the code. Unlike other languages where `this` is tied to the class it was written in, in JS, **`this` is determined by *how* a function is called, not where it is written.**

There are four standard rules for `this` binding, evaluated in order of precedence:

## 1. Default Binding (Standalone Function)
If a function is invoked on its own (e.g., `myFunction()`), `this` points to the global object (`window` in browsers, `global` in Node). 
*Note: In `strict mode`, default binding makes `this` evaluate to `undefined` to prevent accidental global mutations.*

## 2. Implicit Binding (Object Method)
If a function is called as a property of an object, `this` points to the object immediately to the left of the dot.
```javascript
const user = {
  name: "Alice",
  greet() {
    console.log(this.name); // 'this' points to 'user'
  }
};
user.greet(); // "Alice"
```

## 3. Explicit Binding (call, apply, bind)

You can force `this` to point to a specific object by using the built-in `.call()`, `.apply()`, or `.bind()` methods on the function.

JavaScript

```
function greet() { console.log(this.name); }
const user = { name: "Bob" };
greet.call(user); // "Bob"
```

## 4. New Binding (Constructor Function)

When a function is called with the `new` keyword, a brand new object is created in memory, and `this` is pointed to that newly created object.

JavaScript

```
function User(name) {
  this.name = name; // 'this' refers to the new object being constructed
}
const charlie = new User("Charlie");
```

## The Arrow Function Exception

Arrow functions **do not** follow these four rules. They do not have their own `this` binding. Instead, they use Lexical Scoping to inherit `this` from the surrounding parent function.

## Related

- [[js-call-apply-bind-methods]] - Deep dive into explicit binding.
- [[js-arrow-functions-and-lexical-this]] - How arrow functions bypass these rules.