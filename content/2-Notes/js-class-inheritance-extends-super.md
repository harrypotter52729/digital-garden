---
id: 202607280834
title: Class Inheritance (extends and super)
aliases:
  - class inheritance
  - extends keyword
  - super keyword
  - ES6 inheritance
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/oop
  - topic/classes
date_created: 2026-07-28
mastery_level: 1
---

# Class Inheritance (`extends` and `super`)

> **TL;DR:** `BasePage` is like a standard vehicle blueprint containing a chassis, steering wheel, and engine (`goto()`, `takeScreenshot()`). A child class `LoginPage extends BasePage` adds a specialized armored turret (`login()`). Calling `super(page)` inside the child constructor activates the base engine first before installing the turret!

---

## Why This Exists
In large software applications and test automation frameworks (e.g., Playwright or Selenium Page Object Models), many classes share common functionality (such as logging, navigation, waiting, taking screenshots, or handling errors). 

Duplicating shared methods across 100 separate Page Object classes creates a maintenance nightmare. ES6 class inheritance via **`extends`** and **`super`** allows child classes to inherit shared state and methods from a parent class while overriding or expanding functionality where needed.

---

## Mental Model
Think of class inheritance as an architectural building hierarchy:
- **Parent Class (`BasePage`)**: The foundation structure. Defines shared capabilities (`this.page = page`, `.navigate()`, `.takeScreenshot()`).
- **Child Class (`LoginPage extends BasePage`)**: A specialized upper floor built on top of the foundation.
- **`super()` Call**: The structural pillar connecting the child floor to the parent foundation. You **cannot** customize your child floor (`this.username = "#user"`) until `super()` finishes laying the parent foundation!

```
                       ┌─────────────────────────┐
                       │   BasePage (Parent)     │
                       │ - this.page = page      │
                       │ - goto(url)             │
                       │ - takeScreenshot()      │
                       └────────────▲────────────┘
                                    │
                                    │ extends
                                    │
                       ┌────────────┴────────────┐
                       │   LoginPage (Child)     │
                       │ - super(page)           │
                       │ - login(user, pass)     │
                       └─────────────────────────┘
```

---

## How It Works

1. **`extends` Keyword Setup:**
   - Establishes prototypal inheritance between two class prototypes: `Child.prototype.[[js-prototype-chain-mechanics|Prototype]] = Parent.prototype`.
   - Establishes static inheritance between constructors: `Child.[[js-prototype-chain-mechanics|Prototype]] = Parent`.
2. **`super()` in Constructors:**
   - In a derived child class constructor, `this` does **not** exist initially!
   - Calling `super(...args)` invokes the parent class constructor, allocating `this` and executing parent property initialization.
   - Accessing `this` before calling `super()` throws a `ReferenceError: Must call super constructor in derived class before accessing 'this'`.
3. **`super.method()` in Instance Methods:**
   - Invokes a parent method from within an overridden child method (method delegation).

---

## Key Characteristics
- **Mandatory `super()` for Derived Constructors:** Derived classes specifying a `constructor()` **must** call `super()` before referencing `this`.
- **Default Constructor Generation:** If a child class omits its `constructor()`, JavaScript generates one automatically: `constructor(...args) { super(...args); }`.
- **Prototype Chain Link:** Method lookup on a child instance walks up to `Child.prototype`, then to `Parent.prototype`, then to `Object.prototype`.

---

## Common Mistakes

### Mistake 1: Accessing `this` before calling `super()` in a derived class constructor
Attempting to initialize child properties before `super()` throws an immediate exception.

```javascript
class BasePage {
  constructor(page) { this.page = page; }
}

class LoginPage extends BasePage {
  constructor(page) {
    // WRONG: 'this' accessed before super()!
    this.url = "/login"; 
    super(page); // CRASH! ReferenceError!
  }
}
```

### Mistake 2: Creating deep, rigid inheritance chains (Fragile Base Class Problem)
Inheriting through 5+ levels (`BasePage -> AuthPage -> AdminPage -> ReportPage -> SalesReportPage`) creates brittle code where changing a parent method breaks dozens of child classes.

---

## Best Practices
- **Keep parent classes (`BasePage`) lean and generic**, holding only universal capabilities (e.g. navigation, logging, basic waits).
- **Always call `super()` as the first statement** inside derived class constructors.
- **Favor Composition over Inheritance** for non-IS-A relationships (e.g. inject a `Logger` or `ApiClient` helper instead of inheriting from it).

---

## Comparison Table

| Concept | Base Class (`BasePage`) | Derived Class (`LoginPage extends BasePage`) |
| :--- | :--- | :--- |
| **`this` Initialization**| Self-allocated on `new` | **Allocated BY calling `super()`** |
| **Constructor Rule** | Standard constructor | **MUST call `super()` before `this`** |
| **Prototype Link** | `BasePage.prototype -> Object.prototype` | `LoginPage.prototype -> BasePage.prototype` |
| **Static Link** | `BasePage -> Function.prototype` | `LoginPage -> BasePage` (Inherits static methods) |

---

## Canonical Code Example

```javascript
/**
 * Demonstrates ES6 Class Inheritance (extends and super), derived constructors,
 * method overriding, parent method invocation, and static inheritance.
 */

// 1. Parent Class (BasePage for Playwright Automation Framework)
class BasePage {
  constructor(page) {
    if (!page) throw new Error("BasePage requires a valid Playwright page object.");
    this.page = page;
    this.defaultTimeout = 5000;
  }

  // Parent Instance Methods
  async goto(path) {
    console.log(`[BasePage] Navigating to: https://app.com${path}`);
    return `Navigated to ${path}`;
  }

  async takeScreenshot(name) {
    console.log(`[BasePage] Capturing screenshot: '${name}.png'`);
    return `Screenshot ${name}.png saved`;
  }

  // Parent Static Method
  static getFrameworkVersion() {
    return "v2.5.0";
  }
}

// 2. Derived Child Class (LoginPage Page Object Model)
class LoginPage extends BasePage {
  constructor(page, customTimeout) {
    // CRITICAL: Call super() FIRST to initialize parent constructor & 'this'!
    super(page);

    // Initializing Child Specific Properties
    this.usernameSelector = "#username";
    this.passwordSelector = "#password";
    this.submitSelector = "#submit-btn";
    if (customTimeout) this.defaultTimeout = customTimeout;
  }

  // Specialized Child Method
  async login(user, pass) {
    console.log(`[LoginPage] Logging in user: ${user}`);
    await this.goto("/login"); // Inherited from BasePage
    return `User ${user} logged in successfully`;
  }

  // Overriding Parent Method & Delegating via super.takeScreenshot()
  async takeScreenshot(name) {
    console.log(`[LoginPage] Custom login pre-screenshot hook...`);
    // Delegate actual capture work to parent BasePage method!
    return await super.takeScreenshot(`login_${name}`);
  }
}

// 3. Execution & Verification
const mockPage = { id: "playwright_page_001" };
const loginPage = new LoginPage(mockPage, 10000);

// Verifying Inherited Properties & Methods
console.log("Timeout Value:", loginPage.defaultTimeout); // Expected: 10000 (Overridden)

// Calling Inherited Method directly
loginPage.goto("/dashboard"); // Output: [BasePage] Navigating to: https://app.com/dashboard

// Calling Specialized Child Method
loginPage.login("luffy", "gear5"); // Output: Navigates & Logs in

// Calling Overridden Method (Delegates to super.takeScreenshot)
loginPage.takeScreenshot("error_state");
// Output: [LoginPage] Custom login pre-screenshot hook...
// Output: [BasePage] Capturing screenshot: 'login_error_state.png'

// 4. Verifying Static Method Inheritance
console.log("Static Version Check:", LoginPage.getFrameworkVersion()); // Expected: "v2.5.0"

// 5. Prototype Chain Verification
console.log("Is loginPage instance of LoginPage?:", loginPage instanceof LoginPage); // Expected: true
console.log("Is loginPage instance of BasePage?:", loginPage instanceof BasePage);   // Expected: true
console.log("Prototype Link Check:", Object.getPrototypeOf(LoginPage.prototype) === BasePage.prototype); 
// Expected: true
```

---

## Key Takeaways
- `extends` establishes prototypal inheritance between parent and child class prototypes and constructors.
- In derived class constructors, `super()` **must** be called before accessing `this`.
- `super.methodName()` allows child classes to override parent methods while delegating shared work back to the parent.
- Derived classes inherit both instance methods and static methods from parent classes.

---

## Related
- [[js-es6-classes-basics]] — ES6 Class syntax and prototype sugar.
- [[js-composition-vs-inheritance]] — Architectural guide comparing Composition vs Inheritance.
- [[js-prototype-chain-mechanics]] — Low-level engine mechanics behind prototype delegation.
- [[MOC - JS Objects & Structures]] — Central Map of Content for Objects.
