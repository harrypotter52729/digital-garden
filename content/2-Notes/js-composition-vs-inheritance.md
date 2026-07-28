---
id: 202607280835
title: Composition vs Inheritance in JavaScript
aliases:
  - composition vs inheritance
  - IS-A vs HAS-A
  - dependency injection
  - framework architecture
tags:
  - type/concept
  - status/processing
  - lang/js
  - topic/architecture
  - topic/oop
date_created: 2026-07-28
mastery_level: 1
---

# Composition vs Inheritance in JavaScript

> **TL;DR:** Inheritance is an **"IS-A"** relationship (a `SportsCar` IS-A `Vehicle`), which forces child classes to inherit every single item from a monolithic parent class. Composition is a **"HAS-A"** relationship (a `Car` HAS-A `Engine`, HAS-A `GPS`). Instead of inheriting a huge 2,000-line `BasePage`, composition lets you plug in only the small, reusable helper modules your component actually needs!

---

## Why This Exists
In software architecture and test automation frameworks, beginners often rely heavily on Class Inheritance (`extends BasePage`), placing every helper function—logging, database connectors, API request clients, screenshot utilities, report generators, and excel readers—into one giant monolithic `BasePage`.

This causes the **Fragile Base Class Problem** and tight coupling. Modifying `BasePage` to fix an API helper risks breaking 200 unrelated UI Page Objects. **Composition** and **Dependency Injection (DI)** solve this by breaking utilities into single-responsibility modules and plugging them into components as needed.

---

## Mental Model
Think of assembling a computer vs inheriting a house:
- **Monolithic Inheritance:** You inherit a rigid house structure. If you only want a place to sleep, you are forced to inherit the attached swimming pool, commercial kitchen, and underground bunker whether you need them or not.
- **Composition (Modular Assembly):** You buy a custom desktop case. You pick a CPU (`Logger`), plug in a Graphics Card (`ApiClient`), and attach a Monitor (`WaitHelper`). If you want to upgrade your graphics card tomorrow, you swap that single module without rebuilding the case!

```
MONOLITHIC INHERITANCE (Tight Coupling)
[ BasePage (Monolith: DB + API + Logger + Screenshots + Reporting) ]
       ▲
       └── [ LoginPage ] (Inherits ALL bloat unintentionally!)

COMPOSITION & DEPENDENCY INJECTION (Loose Coupling)
[ LoginPage ]
   ├── has-a ──► [ Logger Service ]
   ├── has-a ──► [ ApiClient Service ]
   └── has-a ──► [ Screenshot Utility ]
```

---

## How It Works

### 1. The IS-A vs HAS-A Rule
- **Use Inheritance (IS-A):** Only when a clear, permanent hierarchical taxonomy exists (e.g. `LoginPage` IS-A `BasePage` sharing basic browser navigation).
- **Use Composition (HAS-A):** When combining independent capabilities or services (e.g. `LoginPage` HAS-A `Logger`, `LoginPage` HAS-A `ApiClient`).

### 2. Dependency Injection (DI)
Instead of a class instantiating its helpers internally (`this.logger = new Logger()`), dependencies are passed into the constructor from the outside (`constructor(page, logger)`). This makes components loosely coupled and easily testable using mock objects!

---

## Key Characteristics
- **Single Responsibility Principle (SRP):** Each helper class has one reason to change (e.g., `Logger` handles logging; `ApiClient` handles network calls).
- **Flexibility & Testability:** Swapping a real dependency (`Logger`) for a mock dependency (`MockLogger`) in unit/automation tests requires zero code changes inside the consuming class.

---

## Common Mistakes

### Mistake 1: Inheritance Bloat (Monolithic Base Classes)
Stuffing non-page utilities (e.g., database SQL query helpers, email API handlers) into `BasePage` via inheritance.

```javascript
// BAD: Monolithic BasePage bloated with unrelated services
class BasePage {
  connectToDatabase() { ... } // Why does a UI Page Object need DB queries?
  sendSlackNotification() { ... }
  generatePDFReport() { ... }
}
```

### Mistake 2: Hardcoding dependency instantiation inside constructors
Instantiating helper utilities directly inside class constructors prevents mocking dependencies during testing.

```javascript
// TIGHT COUPLING (Hard to test):
class LoginPage {
  constructor(page) {
    this.logger = new RealCloudLogger(); // Hardcoded tight dependency!
  }
}

// LOOSE COUPLING (Dependency Injection):
class LoginPage {
  constructor(page, logger) {
    this.logger = logger; // Injected from outside!
  }
}
```

---

## Best Practices
- **Keep `BasePage` minimal**, limiting it strictly to universal browser interactions (`goto`, `waitForLoad`, `getPageTitle`).
- **Build small, decoupled helper services** (`Logger`, `ApiClient`, `WaitHelper`, `DatabaseClient`).
- **Inject dependencies via constructor parameters** to maximize testability and flexibility.
- **Use a Hybrid Approach:** Combine a shallow 1-level inheritance hierarchy (`LoginPage extends BasePage`) with composition for helper services.

---

## Comparison Table

| Attribute | Class Inheritance (`extends`) | Composition & Dependency Injection |
| :--- | :--- | :--- |
| **Relationship Type** | **IS-A** (Structural Hierarchy) | **HAS-A** (Service Plug-in) |
| **Coupling Level** | **Tight Coupling** (Parent changes affect all children) | **Loose Coupling** (Modules are independent) |
| **Change Flexibility** | Difficult (Hierarchy refactoring required) | **Easy (Swap helper implementations)** |
| **Testability / Mocking**| Hard (Parent methods locked in) | **Easy (Inject mock helper instances)** |
| **Code Organization** | Risk of monolithic bloat | **Single Responsibility Principle (SRP)**|

---

## Canonical Code Example

```javascript
/**
 * Demonstrates Modular Composition and Dependency Injection (DI)
 * vs Monolithic Inheritance in an Automation Framework.
 */

// 1. Single-Responsibility Helper Services (Composable Modules)
class Logger {
  info(message) {
    console.log(`[INFO ${new Date().toISOString()}] ${message}`);
  }
}

class ApiClient {
  async fetchTestData(endpoint) {
    console.log(`[API] Fetching mock test data from: ${endpoint}`);
    return { userId: 101, token: "mock_token_abc" };
  }
}

class ScreenshotUtility {
  async capture(page, name) {
    console.log(`[SCREENSHOT] Saved: ${name}.png`);
    return `${name}.png`;
  }
}

// 2. Minimal Base Page (Inheritance strictly for true IS-A browser interactions)
class BasePage {
  constructor(page) {
    this.page = page;
  }

  async navigate(url) {
    console.log(`[NAVIGATE] Opening: ${url}`);
  }
}

// 3. Composed Page Object using Dependency Injection (Loose Coupling)
class LoginPage extends BasePage {
  // Helpers injected via constructor parameters!
  constructor(page, logger, apiParams, screenshotUtil) {
    super(page); // Inherits minimal BasePage
    
    // Composed Helper Services (HAS-A Relationships)
    this.logger = logger;
    this.apiClient = apiParams;
    this.screenshot = screenshotUtil;
  }

  async performLogin(username, password) {
    this.logger.info(`Starting login flow for ${username}`);
    
    await this.navigate("/login");
    
    // Fetch seed data via composed API client helper
    const seedData = await this.apiClient.fetchTestData("/user/seed");
    this.logger.info(`Using seed token: ${seedData.token}`);

    // Capture screenshot via composed Utility helper
    await this.screenshot.capture(this.page, "login_success");
    
    return true;
  }
}

// 4. Execution in Production Environment (Injecting Real Services)
const mockPlaywrightPage = { id: "pg_1" };
const realLogger = new Logger();
const realApi = new ApiClient();
const realScreenshot = new ScreenshotUtility();

const loginPage = new LoginPage(mockPlaywrightPage, realLogger, realApi, realScreenshot);
loginPage.performLogin("monk_luffy", "gear5_secret");

// 5. Testing Environment (Injecting Mock Logger for Unit Testing)
const mockLogger = {
  info(msg) { console.log(`[MOCK LOGGER SPY]: ${msg}`); }
};

const testableLoginPage = new LoginPage(mockPlaywrightPage, mockLogger, realApi, realScreenshot);
testableLoginPage.performLogin("test_user", "test_pass");
```

---

## Key Takeaways
- Inheritance models **IS-A** structural relationships; Composition models **HAS-A** capability relationships.
- Avoid stuffing unrelated utilities into a monolithic `BasePage`. Keep base classes small and focused.
- Composition breaks utilities into single-responsibility classes (`Logger`, `ApiClient`, `ScreenshotUtility`).
- **Dependency Injection (DI)** passes helpers into constructors from the outside, making code loosely coupled and easily testable.

---

## Related
- [[js-class-inheritance-extends-super]] — ES6 Class inheritance mechanics.
- [[js-factory-functions]] — Alternative object creation patterns using composition.
- [[js-es6-classes-basics]] — Foundations of ES6 Classes.
- [[MOC - Playwright Framework Design]] — Map of Content for Framework Architecture.
