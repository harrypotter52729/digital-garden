---
id: 202602081404
title: ES6 Classes Under the Hood
aliases: [ES6 Classes, class syntax]
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/oop
date_created: {{date}}
mastery_level: 1
---
# ES6 Classes Under the Hood

In ES6 (2015), JavaScript introduced the `class` keyword. However, this did not change how JavaScript fundamentally works. **ES6 Classes are purely "syntactic sugar"** over traditional Prototypal Inheritance and Constructor Functions.

## The Traditional Way (Before ES6)
To create a "Class", you used a standard function and manually attached methods to its `.prototype`.
```javascript
function User(name) {
  this.name = name; // Instance property
}

// Attach method to the prototype so it's shared across all instances
User.prototype.sayHi = function() {
  console.log("Hi, " + this.name);
};

const bob = new User("Bob");
bob.sayHi();
```

## The ES6 Way

The `class` syntax achieves the exact same result in memory, but looks much cleaner, mimicking traditional OOP languages.



```JavaScript
class User {
  constructor(name) {
    this.name = name; // Instance property (New Binding)
  }

  // JS automatically puts this on User.prototype
  sayHi() {
    console.log("Hi, " + this.name);
  }
}

const bob = new User("Bob");
bob.sayHi();
```

## `extends` and `super()`

- `extends` sets up the prototype chain automatically, linking a child class to a parent class.
- `super()` calls the constructor function of the parent class. In a child class constructor, you _must_ call `super()` before you can use the `this` keyword.

## Related
- [[js-prototypal-inheritance]] - The actual mechanism running behind the `class` keyword.
- [[js-the-this-keyword-rules]] - How the `new` keyword instantiates the class.
* [[pw-page-object-model-pattern]] - Page Object Model pages are structured as JavaScript classes with constructors and prototype methods.
