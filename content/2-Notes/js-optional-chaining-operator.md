---
id: 202606120829
title: Optional Chaining Operator
aliases:
  - optional-chaining
  - safe-navigation
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/syntax
date_created: 2026-06-12
mastery_level: 1
---

# Optional Chaining Operator

> **TL;DR:** Optional chaining (`?.`) is like a **cautious delivery driver** checking if each gate is open before walking through. If any gate along the path is locked (i.e., [[js-null-vs-undefined|null or undefined]]), the driver stops immediately and returns `undefined` instead of crashing your program.

## The Mental Model
Before optional chaining, checking nested objects was tedious:
`user && user.address && user.address.street`

It was like checking: "Does the user exist? Yes. Does the address exist? Yes. Okay, now get the street." If you forgot one check, and the address was missing, JavaScript would throw a fatal error: `TypeError: Cannot read properties of undefined (reading 'street')`.

Optional chaining (`?.`) acts as a safeguard. It reads: "Give me `user?.address?.street`. If at any point the value is [[js-null-vs-undefined|null or undefined]], abort and return `undefined` immediately."

---

## 1. Syntax Variations
Optional chaining can be used in three main scenarios:

### Property Access
Checking nested keys inside objects.
```javascript
const street = user?.address?.street;
```

### Dynamic Property / Array Access (`?.[]`)
Checking item elements in an array or looking up a dynamic key that might not exist.
```javascript
const secondItem = array?.[1];
const value = user?.[dynamicKey];
```

### Function or Method Calls (`?.()`)
Invoking a function only if it actually exists.
```javascript
user.greet?.(); // Only runs greet() if it is defined as a function
```

---

## 2. Pairing with the [[js-difference-between-or-operator-and-nullish-coalescing|Nullish Coalescing Operator (`??`)]]
By default, optional chaining returns `undefined` if a value is missing. You can pair it with `??` to supply a fallback value:

```javascript
const zipcode = user?.address?.zipcode ?? "No zipcode provided";
```

---

## Canonical Code Example

Here is a script demonstrating how to safely extract deeply nested data:

```javascript
// A mixed array of user profiles (some are missing fields)
const users = [
  {
    name: "Alice",
    contact: {
      email: "alice@example.com",
      phone: () => "+123456789"
    }
  },
  {
    name: "Bob",
    // contact is missing!
  }
];

// Let's loop through and query details safely
users.forEach(user => {
  console.log(`User: ${user.name}`);

  // 1. Safe Property Access
  // For Bob, contact is undefined, so contact.email isn't evaluated.
  // It returns undefined instead of throwing a TypeError.
  const email = user.contact?.email ?? "No email listed";
  console.log(`- Email: ${email}`);

  // 2. Safe Function Execution
  // Only invoke phone() if contact and phone exist.
  const phoneNumber = user.contact?.phone?.() ?? "No phone listed";
  console.log(`- Phone: ${phoneNumber}`);
});
```

---

## Related
* [[js-null-vs-undefined]] - Primitives triggering path short-circuiting.
* [[js-difference-between-or-operator-and-nullish-coalescing]] - Providing fallback default values.
* [[MOC - JS Objects & Structures]] - Navigating complex object structures.
