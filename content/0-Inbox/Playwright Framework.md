# Playwright + JavaScript Automation Framework Roadmap

---

## 1. 🌐 Website Selection

### Primary Website: **[Automation Exercise](https://automationexercise.com/)**

| Criteria                            | Coverage on AutomationExercise.com                                                                                 |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| Login / Registration                | Full signup flow with form fields; login with valid/invalid credentials                                            |
| Forms & Validations                 | Contact Us form, checkout forms, review form, subscription — with dropdowns, radio buttons, checkboxes, text areas |
| File Upload                         | Contact Us page allows file attachment                                                                             |
| Search & Filter                     | Product search by name; category sidebar; brand filtering                                                          |
| Shopping Cart / Multi-step Workflow | Add to cart → View cart → Proceed to checkout → Payment → Order confirmation                                       |
| Dynamic Content                     | Product listings rendered dynamically; recommended items carousel                                                  |
| Alerts / Modals                     | JavaScript confirm dialogs, overlay modals on product details (hover)                                              |
| Scroll Interactions                 | Scroll-up/scroll-down arrow, subscription at footer                                                                |
| API Endpoints                       | **14+ documented REST API endpoints** at `https://automationexercise.com/api_list`                                 |
| Responsive Design                   | Fully responsive layout for cross-viewport testing                                                                 |

### Why This Website?

1. **Real-world e-commerce domain** — the most common domain asked about in SDET interviews
2. **End-to-end user journeys** — you can automate complete purchase flows, not just isolated elements
3. **Documented API endpoints** — allows you to do API testing AND combine UI + API tests (e.g., create user via API, then login via UI)
4. **Stable and free** — no login-wall, no rate limiting, no CAPTCHA blocking automation
5. **Rich test case library** — the site itself lists 26+ test cases you can reference, but we will go far beyond those
6. **Portfolio appeal** — interviewers immediately understand an e-commerce automation framework

> **Supplementary resource**: For isolated widget practice (date pickers, complex tables, drag-and-drop, iframes), bookmark [DemoQA](https://demoqa.com/) — we will pull 2–3 exercises from it in later phases.

---

## 2. 📁 Final Folder Structure

text

```
playwright-ecommerce-framework/
│
├── .github/
│   └── workflows/
│       └── playwright.yml              # GitHub Actions CI/CD pipeline
│
├── config/
│   └── environments.js                 # Environment-specific URLs & settings
│
├── pages/                              # Page Object Model classes
│   ├── BasePage.js                     # Shared methods: navigate, waitFor, screenshot
│   ├── HomePage.js                     # Home page locators & actions
│   ├── LoginPage.js                    # Login & signup page
│   ├── SignupPage.js                   # Registration form page
│   ├── ProductsPage.js                # Products listing, search, category
│   ├── ProductDetailPage.js           # Single product detail + review
│   ├── CartPage.js                    # Shopping cart
│   ├── CheckoutPage.js               # Address verification + payment
│   └── ContactUsPage.js              # Contact form with file upload
│
├── tests/                             # Test spec files grouped by feature
│   ├── auth/
│   │   ├── login.spec.js
│   │   └── registration.spec.js
│   ├── products/
│   │   ├── productSearch.spec.js
│   │   └── productDetails.spec.js
│   ├── cart/
│   │   └── cartWorkflow.spec.js
│   ├── checkout/
│   │   └── endToEndCheckout.spec.js
│   ├── contact/
│   │   └── contactUs.spec.js
│   ├── api/
│   │   ├── userApi.spec.js
│   │   └── productApi.spec.js
│   └── visual/
│       └── homepage.visual.spec.js
│
├── test-data/                         # External test data files
│   ├── loginData.json
│   ├── registrationData.json
│   ├── products.json
│   └── testFiles/
│       └── sample-upload.txt          # File for upload tests
│
├── utils/                             # Reusable helper functions
│   ├── helpers.js                     # Common utilities (random email, date, etc.)
│   ├── apiHelpers.js                  # API request wrapper functions
│   ├── testDataGenerator.js           # Faker.js dynamic data generation
│   └── constants.js                   # App-wide constants (URLs, messages)
│
├── fixtures/                          # Playwright custom fixtures
│   └── pomFixtures.js                 # Fixture that injects page objects
│
├── reports/                           # Generated reports (gitignored)
├── screenshots/                       # Failure screenshots (gitignored)
│
├── .env                               # Environment variables (gitignored)
├── .env.example                       # Template for .env (committed)
├── .gitignore
├── playwright.config.js               # Master Playwright configuration
├── package.json
└── README.md                          # Professional documentation
```

### Folder Purpose Reference

|Folder/File|Purpose|
|---|---|
|`pages/`|Page Object Model — each file encapsulates locators + actions for one page|
|`pages/BasePage.js`|Parent class with shared helpers inherited by all page objects|
|`tests/`|Organized by feature area; each `.spec.js` file is an independent test suite|
|`test-data/`|Externalized test data (JSON/CSV); keeps tests clean and data-driven|
|`utils/`|Stateless helper functions reusable across tests and pages|
|`fixtures/`|Playwright custom fixtures — dependency injection for page objects|
|`config/`|Environment-specific configuration (staging URL vs production URL, etc.)|
|`.github/workflows/`|CI/CD pipeline definitions for automated runs on push/PR|
|`reports/` & `screenshots/`|Output artifacts — always gitignored|

---

## 3. 🛠️ Tech Stack & Tools

|Tool / Library|Install Command|Why You Need It|
|---|---|---|
|**Playwright Test**|`npm init playwright@latest`|Core framework — test runner, browser automation, assertions, built-in waits|
|**dotenv**|`npm i dotenv`|Load `.env` variables (URLs, credentials) — never hardcode secrets|
|**@faker-js/faker**|`npm i @faker-js/faker`|Generate realistic random test data (names, emails, addresses) on every run|
|**csv-parser**|`npm i csv-parser`|Parse CSV files for data-driven testing with large datasets|
|**allure-playwright**|`npm i -D allure-playwright allure-commandline`|Industry-standard rich test reports with steps, attachments, history|
|**@playwright/test** (built-in)|Included|HTML reporter, screenshot/video on failure — zero config|
|**eslint**|`npm i -D eslint`|Code quality & consistency — expected in professional projects|
|**prettier**|`npm i -D prettier`|Auto-format code — consistent style across all files|
|**husky** _(optional)_|`npm i -D husky`|Pre-commit hooks — run linter before each commit|
|**cross-env**|`npm i -D cross-env`|Set environment variables cross-platform in npm scripts|

---

## 4. 📋 Phased Task List — 30-Day Roadmap

---

### 📌 PHASE 1: Project Setup & First Linear Tests

**Objective**: Initialize the project, configure Playwright, and write your first 8 working tests as simple linear scripts (no POM yet).

**Timeline**: Days 1–4 (4 days)

#### Tasks

| #    | Test Case                                                                                                                                                            | What You Practice                                                                     |
| ---- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| 1.1  | Initialize project: `npm init playwright@latest`, choose JS, install browsers, verify sample test runs                                                               | Playwright CLI, project scaffolding                                                   |
| 1.2  | Configure `playwright.config.js`: set `baseURL` to `https://automationexercise.com`, set default timeout to 30s, enable screenshot on failure only, set retries to 0 | Configuration fundamentals                                                            |
| 1.3  | **Test**: Navigate to homepage → Verify page title contains "Automation Exercise" and the hero slider is visible                                                     | `page.goto()`, `expect(page).toHaveTitle()`, `toBeVisible()`                          |
| 1.4  | **Test**: Navigate to Login page → Enter valid email + valid password → Verify "Logged in as [username]" is visible in the navbar                                    | `page.fill()`, `page.click()`, text assertions                                        |
| 1.5  | **Test**: Navigate to Login page → Enter valid email + INCORRECT password → Verify error message "Your email or password is incorrect!" is displayed                 | Negative testing, error message assertions                                            |
| 1.6  | **Test**: Navigate to Login page → Leave both fields empty → Click Login → Verify HTML5 validation prevents submission                                               | Browser-native validation testing                                                     |
| 1.7  | **Test**: Navigate to Signup page → Enter name + NEW email → Fill complete registration form (title, name, DOB, address) → Verify "ACCOUNT CREATED!" message         | Multi-step form filling, dropdowns (`page.selectOption()`), radio buttons, checkboxes |
| 1.8  | **Test**: Navigate to Signup page → Enter name + ALREADY REGISTERED email → Verify error "Email Address already exist!"                                              | Negative testing, pre-condition awareness                                             |
| 1.9  | **Test**: Click "Contact Us" → Fill name, email, subject, message → Upload a file → Click Submit → Handle browser dialog → Verify success message                    | `page.setInputFiles()`, `page.on('dialog')`, alert handling                           |
| 1.10 | **Test**: After login, click Logout → Verify user is redirected to the Login page                                                                                    | State management, session cleanup                                                     |

#### Framework Concepts Introduced

- Playwright Test runner (`test`, `expect`)
- `playwright.config.js` fundamentals
- Locator strategies: `getByRole()`, `getByText()`, `getByPlaceholder()`, `locator()` with CSS/XPath
- Actions: `click()`, `fill()`, `selectOption()`, `check()`, `setInputFiles()`
- Assertions: `toBeVisible()`, `toHaveTitle()`, `toHaveURL()`, `toContainText()`
- Dialog handling
- `async/await` patterns

#### Acceptance Criteria

- [ ]  All 8 tests pass when you run `npx playwright test`
- [ ]  `playwright.config.js` has custom `baseURL`, timeout, and screenshot settings
- [ ]  Tests run in headed mode (`--headed`) and you can watch them execute
- [ ]  You understand the HTML report: run `npx playwright show-report`

#### Starter `playwright.config.js`

JavaScript

```
// @ts-check
const { defineConfig, devices } = require('@playwright/test');

module.exports = defineConfig({
  testDir: './tests',
  fullyParallel: false,
  forbidOnly: !!process.env.CI,
  retries: 0,
  workers: 1,
  reporter: 'html',
  
  use: {
    baseURL: 'https://automationexercise.com',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
    video: 'off',
    actionTimeout: 15000,
  },

  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
  ],
});
```

#### 🎤 Interview Questions — Phase 1

|#|Question|
|---|---|
|1|**What is the difference between `page.locator()`, `page.getByRole()`, and `page.getByTestId()`? When would you use each?**|
|2|**How does Playwright handle waits differently from Selenium? What is auto-waiting?**|
|3|**Explain the difference between `toBeVisible()`, `toBeAttached()`, and `toBeEnabled()`. Give a scenario for each.**|

---

### 📌 PHASE 2: Page Object Model (POM) Implementation

**Objective**: Refactor all Phase 1 tests into a clean Page Object Model architecture. Introduce a `BasePage` class.

**Timeline**: Days 5–8 (4 days)

#### Tasks

|#|Task|What You Practice|
|---|---|---|
|2.1|Create `BasePage.js` with constructor accepting `page`, and shared methods: `navigateTo(path)`, `getTitle()`, `waitForPageLoad()`, `takeScreenshot(name)`|Inheritance, encapsulation|
|2.2|Create `HomePage.js` extending BasePage — locators for navbar links, slider, category sidebar, footer subscription|Locator encapsulation|
|2.3|Create `LoginPage.js` — locators + methods: `login(email, password)`, `getErrorMessage()`, `navigateToSignup(name, email)`|Method abstraction|
|2.4|Create `SignupPage.js` — locators + method: `fillRegistrationForm(userData)` accepting an object with all fields|Complex form abstraction|
|2.5|Create `ContactUsPage.js` — locators + method: `submitContactForm(data, filePath)`|File upload abstraction|
|2.6|Refactor ALL Phase 1 tests to use page objects — no raw locators in test files|Separation of concerns|
|2.7|Create custom Playwright fixture in `fixtures/pomFixtures.js` that injects page objects into tests|`test.extend()`, dependency injection|
|2.8|**New Test**: Verify all navbar links ("Home", "Products", "Cart", "Contact Us", "Test Cases") navigate to correct URLs|POM method reuse across tests|

#### Example: `BasePage.js`

JavaScript

```
class BasePage {
  constructor(page) {
    this.page = page;
  }

  async navigateTo(path = '/') {
    await this.page.goto(path);
  }

  async getPageTitle() {
    return await this.page.title();
  }

  async waitForPageLoad() {
    await this.page.waitForLoadState('domcontentloaded');
  }

  async takeScreenshot(name) {
    await this.page.screenshot({ path: `screenshots/${name}.png`, fullPage: true });
  }
}

module.exports = BasePage;
```

#### Example: `LoginPage.js`

JavaScript

```
const BasePage = require('./BasePage');

class LoginPage extends BasePage {
  constructor(page) {
    super(page);
    // Login form locators
    this.loginEmailInput = page.locator('[data-qa="login-email"]');
    this.loginPasswordInput = page.locator('[data-qa="login-password"]');
    this.loginButton = page.locator('[data-qa="login-button"]');
    this.loginErrorMessage = page.locator('p[style*="color: red"]');

    // Signup form locators
    this.signupNameInput = page.locator('[data-qa="signup-name"]');
    this.signupEmailInput = page.locator('[data-qa="signup-email"]');
    this.signupButton = page.locator('[data-qa="signup-button"]');
    this.signupErrorMessage = page.locator('p[style*="color: red"]');
  }

  async login(email, password) {
    await this.loginEmailInput.fill(email);
    await this.loginPasswordInput.fill(password);
    await this.loginButton.click();
  }

  async getLoginErrorMessage() {
    return await this.loginErrorMessage.textContent();
  }

  async navigateToSignup(name, email) {
    await this.signupNameInput.fill(name);
    await this.signupEmailInput.fill(email);
    await this.signupButton.click();
  }
}

module.exports = LoginPage;
```

#### Example: Custom Fixture — `fixtures/pomFixtures.js`

JavaScript

```
const base = require('@playwright/test');
const HomePage = require('../pages/HomePage');
const LoginPage = require('../pages/LoginPage');
const ProductsPage = require('../pages/ProductsPage');

exports.test = base.test.extend({
  homePage: async ({ page }, use) => {
    await use(new HomePage(page));
  },
  loginPage: async ({ page }, use) => {
    await use(new LoginPage(page));
  },
  productsPage: async ({ page }, use) => {
    await use(new ProductsPage(page));
  },
});

exports.expect = base.expect;
```

#### Usage in Test File

JavaScript

```
const { test, expect } = require('../fixtures/pomFixtures');

test('Login with valid credentials', async ({ loginPage, page }) => {
  await loginPage.navigateTo('/login');
  await loginPage.login('validuser@test.com', 'validpassword');
  await expect(page.locator('a').filter({ hasText: 'Logged in as' })).toBeVisible();
});
```

#### Acceptance Criteria

- [ ]  Zero raw locators exist inside any `.spec.js` test file
- [ ]  `BasePage.js` is inherited by every page object
- [ ]  Custom fixture injects at least 4 page objects
- [ ]  All Phase 1 tests still pass after refactoring
- [ ]  Each page object file has methods with clear, descriptive names

#### 🎤 Interview Questions — Phase 2

|#|Question|
|---|---|
|1|**What is the Page Object Model and why is it important in test automation? What problems does it solve?**|
|2|**How do Playwright fixtures differ from `beforeEach` hooks? Why would you use fixtures for page objects?**|
|3|**If two pages share a common component (e.g., a navbar), how would you design your page objects to avoid duplication?**|

---

### 📌 PHASE 3: Data-Driven Testing

**Objective**: Externalize all hardcoded test data. Implement parameterized tests using JSON, CSV, and dynamic data generation.

**Timeline**: Days 9–11 (3 days)

#### Tasks

|#|Task|What You Practice|
|---|---|---|
|3.1|Install `@faker-js/faker`. Create `utils/testDataGenerator.js` with functions: `generateUser()`, `generateAddress()`, `generateContactMessage()` — each returns an object with random realistic data|Dynamic data generation|
|3.2|Create `test-data/loginData.json` with multiple test scenarios (valid credentials, wrong password, empty fields, SQL injection string, XSS string)|JSON test data externalization|
|3.3|Refactor login tests to use `test.describe` + loop through `loginData.json` — each data entry becomes a separate test|Data-driven pattern with JSON|
|3.4|Refactor registration test to use `faker`-generated data instead of hardcoded values — every run creates a unique user|Faker integration|
|3.5|Create `test-data/searchTerms.json` with: valid product name, partial match, non-existent product, special characters — write parameterized search tests|Parameterized testing|
|3.6|Create `.env` file with `BASE_URL`, `VALID_EMAIL`, `VALID_PASSWORD`. Create `.env.example` as template. Install `dotenv`. Update `playwright.config.js` to read from `.env`|Environment variable management|
|3.7|Create `test-data/registrationData.csv` with 3 rows of registration data. Write a utility to parse CSV and feed into tests|CSV-driven testing|

#### Example: `test-data/loginData.json`

JSON

```
[
  {
    "scenario": "Valid login with correct credentials",
    "email": "USE_ENV",
    "password": "USE_ENV",
    "shouldSucceed": true,
    "expectedMessage": "Logged in as"
  },
  {
    "scenario": "Invalid login with incorrect password",
    "email": "USE_ENV",
    "password": "wrongpassword123",
    "shouldSucceed": false,
    "expectedMessage": "Your email or password is incorrect!"
  },
  {
    "scenario": "Invalid login with unregistered email",
    "email": "nonexistent_user_xyz@test.com",
    "password": "anypassword",
    "shouldSucceed": false,
    "expectedMessage": "Your email or password is incorrect!"
  }
]
```

#### Example: `utils/testDataGenerator.js`

JavaScript

```
const { faker } = require('@faker-js/faker');

function generateUser() {
  return {
    name: faker.person.firstName() + ' ' + faker.person.lastName(),
    email: `testuser_${Date.now()}@test.com`,
    password: faker.internet.password({ length: 12 }),
    firstName: faker.person.firstName(),
    lastName: faker.person.lastName(),
    company: faker.company.name(),
    address: faker.location.streetAddress(),
    city: faker.location.city(),
    state: faker.location.state(),
    zipcode: faker.location.zipCode(),
    phone: faker.phone.number(),
  };
}

function generateContactMessage() {
  return {
    name: faker.person.fullName(),
    email: faker.internet.email(),
    subject: faker.lorem.sentence(4),
    message: faker.lorem.paragraph(2),
  };
}

module.exports = { generateUser, generateContactMessage };
```

#### Example: Data-Driven Login Test

JavaScript

```
const { test, expect } = require('../fixtures/pomFixtures');
const loginData = require('../test-data/loginData.json');
require('dotenv').config();

for (const data of loginData) {
  test(`Login: ${data.scenario}`, async ({ loginPage, page }) => {
    const email = data.email === 'USE_ENV' ? process.env.VALID_EMAIL : data.email;
    const password = data.password === 'USE_ENV' ? process.env.VALID_PASSWORD : data.password;

    await loginPage.navigateTo('/login');
    await loginPage.login(email, password);

    if (data.shouldSucceed) {
      await expect(page.getByText(data.expectedMessage)).toBeVisible();
    } else {
      const errorMsg = await loginPage.getLoginErrorMessage();
      expect(errorMsg).toContain(data.expectedMessage);
    }
  });
}
```

#### Acceptance Criteria

- [ ]  Zero hardcoded test data inside any test file or page object
- [ ]  `.env` file is gitignored; `.env.example` is committed
- [ ]  Registration test uses Faker and passes on every run with unique data
- [ ]  Login test runs as 3+ separate tests from a single data file
- [ ]  Search test is parameterized with 4+ search terms

#### 🎤 Interview Questions — Phase 3

|#|Question|
|---|---|
|1|**What is data-driven testing? What are the benefits and when would you NOT use it?**|
|2|**How do you handle sensitive test data like passwords and API keys in an automation framework?**|
|3|**Explain how you would use the same test logic to test against staging, QA, and production environments.**|

---

### 📌 PHASE 4: Custom Utilities & Helpers

**Objective**: Build reusable utility functions that eliminate code duplication and simplify complex operations.

**Timeline**: Days 12–14 (3 days)

#### Tasks

|#|Task|What You Practice|
|---|---|---|
|4.1|Add to `BasePage.js`: `waitForElement(locator, timeout)`, `isElementVisible(locator)`, `scrollToElement(locator)`, `getTextList(locator)` (returns array of text from a list of elements)|Reusable base methods|
|4.2|Create `utils/helpers.js`: `generateTimestamp()`, `generateUniqueEmail()`, `sanitizeString(str)`, `waitForSeconds(n)`|Stateless utility functions|
|4.3|Create `utils/constants.js`: export all expected messages (error messages, success messages, page titles) as named constants|Magic string elimination|
|4.4|Create an `auth.fixture.js` — a fixture that provides an ALREADY-LOGGED-IN page (performs login in fixture setup, so tests start authenticated)|Advanced fixtures, test isolation|
|4.5|**New Test**: Add product to cart → View cart → Verify product name, price, quantity, and total price are correct|Cart page object, arithmetic assertions|
|4.6|**New Test**: Add 3 different products to cart → Verify all 3 appear in cart with correct individual prices → Verify subtotals|Multi-product workflow, array assertions|
|4.7|**New Test**: Scroll to footer → Enter email in subscription field → Click subscribe → Verify success message "You have been successfully subscribed!"|Scroll actions, footer interactions|
|4.8|Add `test.beforeEach()` and `test.afterEach()` hooks for common setup/teardown (e.g., accepting cookie consent, clearing cart)|Test lifecycle hooks|

#### Example: Auth Fixture (Pre-Authenticated State)

JavaScript

```
const base = require('@playwright/test');

exports.test = base.test.extend({
  authenticatedPage: async ({ page }, use) => {
    await page.goto('/login');
    await page.locator('[data-qa="login-email"]').fill(process.env.VALID_EMAIL);
    await page.locator('[data-qa="login-password"]').fill(process.env.VALID_PASSWORD);
    await page.locator('[data-qa="login-button"]').click();
    await page.waitForURL('**/');
    await use(page);
    // Teardown: logout after test
    await page.goto('/logout');
  },
});
```

#### Example: `utils/constants.js`

JavaScript

```
module.exports = {
  URLS: {
    HOME: '/',
    LOGIN: '/login',
    PRODUCTS: '/products',
    CART: '/view_cart',
    CONTACT: '/contact_us',
  },
  MESSAGES: {
    LOGIN_ERROR: 'Your email or password is incorrect!',
    SIGNUP_EXISTS: 'Email Address already exist!',
    ACCOUNT_CREATED: 'ACCOUNT CREATED!',
    ACCOUNT_DELETED: 'ACCOUNT DELETED!',
    SUBSCRIPTION_SUCCESS: 'You have been successfully subscribed!',
    CONTACT_SUCCESS: 'Success! Your details have been submitted successfully.',
  },
  PAGE_TITLES: {
    HOME: 'Automation Exercise',
  },
};
```

#### Acceptance Criteria

- [ ]  No string literals for expected messages in test files — all come from `constants.js`
- [ ]  `auth.fixture.js` allows writing tests that skip the login step
- [ ]  Cart tests validate price arithmetic (not just presence of elements)
- [ ]  `helpers.js` has at least 4 reusable functions used across multiple test files
- [ ]  `beforeEach` / `afterEach` hooks exist where appropriate

#### 🎤 Interview Questions — Phase 4

|#|Question|
|---|---|
|1|**What are Playwright fixtures and how do they differ from `beforeEach`? When would you use one over the other?**|
|2|**How would you design your framework to ensure tests are independent and can run in any order?**|
|3|**Explain the concept of test isolation. How does Playwright achieve it by default, and where can it break down?**|

---

### 📌 PHASE 5: Reporting, Screenshots & Videos

**Objective**: Configure multi-format reporting with rich failure diagnostics — screenshots, videos, and trace files.

**Timeline**: Days 15–16 (2 days)

#### Tasks

|#|Task|What You Practice|
|---|---|---|
|5.1|Configure built-in HTML reporter with `open: 'never'` (for CI compatibility). Run tests and review the generated report|Built-in reporting|
|5.2|Install and configure **Allure Reporter**: `npm i -D allure-playwright allure-commandline`. Add `['allure-playwright']` to reporters. Run tests → generate Allure report → open it|Industry-standard reporting|
|5.3|Add Allure step annotations in page object methods using `test.step()` so reports show human-readable steps like "Fill login email"|Step-level reporting|
|5.4|Configure `playwright.config.js`: `screenshot: 'only-on-failure'`, `video: 'retain-on-failure'`, `trace: 'on-first-retry'`|Failure diagnostics|
|5.5|Write a deliberately failing test → Run it → Verify screenshot + video are captured → Open the trace file with `npx playwright show-trace`|Trace viewer debugging|
|5.6|Add multiple reporters simultaneously: `reporter: [['html', { open: 'never' }], ['allure-playwright'], ['list']]`|Multi-reporter configuration|
|5.7|Add npm scripts to `package.json`: `test`, `test:headed`, `test:report`, `test:allure-generate`, `test:allure-open`|Script standardization|

#### Updated `playwright.config.js` Snippet

JavaScript

```
reporter: [
  ['list'],
  ['html', { outputFolder: 'reports/html-report', open: 'never' }],
  ['allure-playwright', { outputFolder: 'reports/allure-results' }],
],

use: {
  screenshot: 'only-on-failure',
  video: 'retain-on-failure',
  trace: 'on-first-retry',
},
```

#### npm Scripts in `package.json`

JSON

```
{
  "scripts": {
    "test": "npx playwright test",
    "test:headed": "npx playwright test --headed",
    "test:chromium": "npx playwright test --project=chromium",
    "test:debug": "npx playwright test --debug",
    "report:html": "npx playwright show-report reports/html-report",
    "report:allure-generate": "npx allure generate reports/allure-results -o reports/allure-report --clean",
    "report:allure-open": "npx allure open reports/allure-report"
  }
}
```

#### Acceptance Criteria

- [ ]  `npx playwright test` generates both HTML and Allure reports
- [ ]  Allure report shows step-by-step breakdowns for each test
- [ ]  Failed tests automatically have screenshots and video attached
- [ ]  Trace viewer opens and shows timeline, network calls, DOM snapshots
- [ ]  All report directories are listed in `.gitignore`

#### 🎤 Interview Questions — Phase 5

|#|Question|
|---|---|
|1|**What is a Playwright Trace file and how do you use it to debug flaky tests?**|
|2|**Describe how you would set up reporting in a CI/CD pipeline so stakeholders can view results after each run.**|
|3|**What diagnostic artifacts would you capture when a test fails in CI? How would you configure that?**|

---

### 📌 PHASE 6: API Testing with Playwright

**Objective**: Use Playwright's `APIRequestContext` to test the Automation Exercise REST API endpoints. Build hybrid tests combining API setup with UI verification.

**Timeline**: Days 17–19 (3 days)

#### API Endpoints Reference: `https://automationexercise.com/api_list`

#### Tasks

|#|Test Case|What You Practice|
|---|---|---|
|6.1|Create `utils/apiHelpers.js` with a wrapper class for common API operations: `get(endpoint)`, `post(endpoint, data)`, `put(endpoint, data)`, `delete(endpoint, data)`|API abstraction layer|
|6.2|**API Test**: `GET /api/productsList` → Verify response status is 200 → Verify response body contains a list of products with expected fields (id, name, price, category)|GET request, response validation|
|6.3|**API Test**: `POST /api/productsList` → Verify response status 405 and message "This request method is not supported"|Method-not-allowed testing|
|6.4|**API Test**: `GET /api/brandsList` → Verify response contains brands → Verify each brand has `id` and `brand` fields|Array validation, schema-like checks|
|6.5|**API Test**: `POST /api/searchProduct` → Send `search_product: "top"` → Verify response contains only products matching the search term|POST with form data, filtered results|
|6.6|**API Test**: `POST /api/searchProduct` without `search_product` parameter → Verify 400-level response with appropriate error|Negative API testing|
|6.7|**API Test**: `POST /api/createAccount` → Send full user details → Verify account created (status 201) → Then `DELETE /api/deleteAccount` to clean up|Full CRUD lifecycle|
|6.8|**API Test**: `POST /api/verifyLogin` → Send valid email + password → Verify success response|Authentication API testing|
|6.9|**API Test**: `POST /api/verifyLogin` → Send without email field → Verify error response|Missing parameter testing|
|6.10|**Hybrid Test**: Create a user via `POST /api/createAccount` → Login via UI with that user → Verify "Logged in as" → Delete user via `DELETE /api/deleteAccount`|API + UI hybrid pattern|

#### Example: `utils/apiHelpers.js`

JavaScript

```
class ApiHelper {
  constructor(request) {
    this.request = request;
    this.baseURL = 'https://automationexercise.com/api';
  }

  async getAllProducts() {
    const response = await this.request.get(`${this.baseURL}/productsList`);
    return response;
  }

  async searchProduct(searchTerm) {
    const response = await this.request.post(`${this.baseURL}/searchProduct`, {
      form: { search_product: searchTerm },
    });
    return response;
  }

  async createAccount(userData) {
    const response = await this.request.post(`${this.baseURL}/createAccount`, {
      form: userData,
    });
    return response;
  }

  async deleteAccount(email, password) {
    const response = await this.request.delete(`${this.baseURL}/deleteAccount`, {
      form: { email, password },
    });
    return response;
  }

  async verifyLogin(email, password) {
    const response = await this.request.post(`${this.baseURL}/verifyLogin`, {
      form: { email, password },
    });
    return response;
  }
}

module.exports = ApiHelper;
```

#### Example: API Test Spec

JavaScript

```
const { test, expect } = require('@playwright/test');
const ApiHelper = require('../../utils/apiHelpers');

let api;

test.beforeEach(async ({ request }) => {
  api = new ApiHelper(request);
});

test.describe('Products API Tests', () => {
  test('GET /api/productsList returns 200 with products', async () => {
    const response = await api.getAllProducts();
    expect(response.status()).toBe(200);

    const body = JSON.parse(await response.text());
    expect(body.responseCode).toBe(200);
    expect(body.products.length).toBeGreaterThan(0);
    expect(body.products[0]).toHaveProperty('id');
    expect(body.products[0]).toHaveProperty('name');
    expect(body.products[0]).toHaveProperty('price');
  });

  test('POST /api/searchProduct with valid term returns matching products', async () => {
    const response = await api.searchProduct('top');
    const body = JSON.parse(await response.text());
    
    expect(body.responseCode).toBe(200);
    expect(body.products.length).toBeGreaterThan(0);
    body.products.forEach((product) => {
      expect(product.name.toLowerCase()).toContain('top');
    });
  });
});
```

#### Example: Hybrid Test (API Setup + UI Verification)

JavaScript

```
const { test, expect } = require('@playwright/test');
const ApiHelper = require('../../utils/apiHelpers');
const { generateUser } = require('../../utils/testDataGenerator');

test('Hybrid: Create user via API, login via UI, delete via API', async ({ page, request }) => {
  const api = new ApiHelper(request);
  const user = generateUser();

  // API: Create account
  const createResponse = await api.createAccount({
    name: user.name,
    email: user.email,
    password: user.password,
    title: 'Mr',
    birth_date: '15',
    birth_month: '6',
    birth_year: '1990',
    firstname: user.firstName,
    lastname: user.lastName,
    company: user.company,
    address1: user.address,
    country: 'United States',
    state: user.state,
    city: user.city,
    zipcode: user.zipcode,
    mobile_number: user.phone,
  });
  const createBody = JSON.parse(await createResponse.text());
  expect(createBody.responseCode).toBe(201);

  // UI: Login with the created user
  await page.goto('/login');
  await page.locator('[data-qa="login-email"]').fill(user.email);
  await page.locator('[data-qa="login-password"]').fill(user.password);
  await page.locator('[data-qa="login-button"]').click();
  await expect(page.getByText(`Logged in as ${user.name}`)).toBeVisible();

  // API: Cleanup — delete account
  const deleteResponse = await api.deleteAccount(user.email, user.password);
  const deleteBody = JSON.parse(await deleteResponse.text());
  expect(deleteBody.responseCode).toBe(200);
});
```

#### Acceptance Criteria

- [ ]  At least 8 pure API tests covering GET, POST, PUT, DELETE
- [ ]  At least 1 hybrid test (API create → UI verify → API delete)
- [ ]  `apiHelpers.js` is a reusable wrapper, not raw `request` calls in tests
- [ ]  Response status codes AND response body content are asserted
- [ ]  Negative API tests cover missing parameters and wrong methods

#### 🎤 Interview Questions — Phase 6

|#|Question|
|---|---|
|1|**How does Playwright's `APIRequestContext` differ from tools like Postman or Axios? What advantages does it offer for test automation?**|
|2|**What is a hybrid test? Give an example of when you would use API calls to set up test preconditions for a UI test.**|
|3|**How would you validate an API response schema in your tests without using a dedicated schema validation library?**|

---

### 📌 PHASE 7: Visual Regression Testing

**Objective**: Implement screenshot-based visual comparison testing to catch unintended UI changes.

**Timeline**: Days 20–21 (2 days)

#### Tasks

|#|Task|What You Practice|
|---|---|---|
|7.1|**Visual Test**: Capture and compare full-page screenshot of the Homepage|`toHaveScreenshot()`, baseline generation|
|7.2|**Visual Test**: Capture Login page screenshot — mask the dynamic ad elements using `mask` option|Masking dynamic content|
|7.3|**Visual Test**: Capture the Products listing page — set `maxDiffPixelRatio: 0.05` for tolerance|Threshold configuration|
|7.4|**Visual Test**: Capture a single product card component (element-level screenshot, not full page)|Element-level visual testing|
|7.5|Run tests first time to generate baselines (`--update-snapshots`). Run again to verify they pass. Modify a locator's style via `page.evaluate()` to simulate a visual change → Verify test fails|Snapshot update workflow|
|7.6|Configure snapshot directory in `playwright.config.js`: `snapshotPathTemplate`|Snapshot organization|
|7.7|Add `*.png` snapshot files to version control with a note in README explaining the visual testing workflow|Baseline management documentation|

#### Example: Visual Test

JavaScript

```
const { test, expect } = require('@playwright/test');

test.describe('Visual Regression Tests', () => {
  test('Homepage visual snapshot', async ({ page }) => {
    await page.goto('/');
    await page.waitForLoadState('networkidle');
    
    await expect(page).toHaveScreenshot('homepage.png', {
      fullPage: true,
      maxDiffPixelRatio: 0.05,
      mask: [page.locator('.recommended_items')], // mask carousel — it rotates
    });
  });

  test('Login page visual snapshot', async ({ page }) => {
    await page.goto('/login');
    await page.waitForLoadState('networkidle');
    
    await expect(page).toHaveScreenshot('login-page.png', {
      mask: [page.locator('iframe')], // mask any ad iframes
    });
  });

  test('Product card component visual snapshot', async ({ page }) => {
    await page.goto('/products');
    const firstProduct = page.locator('.productinfo').first();
    
    await expect(firstProduct).toHaveScreenshot('product-card.png');
  });
});
```

#### Acceptance Criteria

- [ ]  At least 4 visual tests covering full-page and element-level screenshots
- [ ]  Baseline snapshots are generated and committed to the repo
- [ ]  Dynamic areas are masked to prevent false failures
- [ ]  `maxDiffPixelRatio` is configured for reasonable tolerance
- [ ]  You can explain the `--update-snapshots` workflow

#### 🎤 Interview Questions — Phase 7

|#|Question|
|---|---|
|1|**What is visual regression testing and how does it complement functional testing?**|
|2|**How do you handle dynamic content (ads, timestamps, carousels) in visual regression tests?**|
|3|**What is `maxDiffPixelRatio` and how would you decide on an appropriate threshold?**|

---

### 📌 PHASE 8: CI/CD Integration with GitHub Actions

**Objective**: Configure a GitHub Actions pipeline that runs all tests automatically on push and pull requests, publishes reports, and uploads artifacts.

**Timeline**: Days 22–23 (2 days)

#### Tasks

|#|Task|What You Practice|
|---|---|---|
|8.1|Create `.github/workflows/playwright.yml` — basic pipeline that installs deps, installs browsers, runs all tests on `push` to `main`|GitHub Actions fundamentals|
|8.2|Add artifact upload step — upload HTML report, Allure results, and screenshots as downloadable artifacts|Artifact management|
|8.3|Add GitHub Actions secrets for `VALID_EMAIL` and `VALID_PASSWORD` — reference them as `${{ secrets.VALID_EMAIL }}`|Secret management in CI|
|8.4|Add a separate job for API-only tests with tag filtering: `npx playwright test --grep @api`|Test tagging + selective CI runs|
|8.5|Configure the pipeline to run on Pull Requests AND on a daily schedule (cron)|Scheduled test execution|
|8.6|Add status badge to README: `![Playwright Tests](https://github.com/USERNAME/REPO/actions/workflows/playwright.yml/badge.svg)`|Professional README|

#### `.github/workflows/playwright.yml`

YAML

```
name: Playwright Tests

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 8 * * 1-5'  # Weekdays at 8 AM UTC

jobs:
  test:
    timeout-minutes: 30
    runs-on: ubuntu-latest
    
    strategy:
      fail-fast: false
      matrix:
        project: [chromium, firefox]

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps ${{ matrix.project }}

      - name: Create .env file
        run: |
          echo "BASE_URL=${{ vars.BASE_URL || 'https://automationexercise.com' }}" >> .env
          echo "VALID_EMAIL=${{ secrets.VALID_EMAIL }}" >> .env
          echo "VALID_PASSWORD=${{ secrets.VALID_PASSWORD }}" >> .env

      - name: Run Playwright tests
        run: npx playwright test --project=${{ matrix.project }}

      - name: Upload HTML Report
        uses: actions/upload-artifact@v4
        if: ${{ !cancelled() }}
        with:
          name: playwright-report-${{ matrix.project }}
          path: reports/html-report/
          retention-days: 14

      - name: Upload Allure Results
        uses: actions/upload-artifact@v4
        if: ${{ !cancelled() }}
        with:
          name: allure-results-${{ matrix.project }}
          path: reports/allure-results/
          retention-days: 14

      - name: Upload failure screenshots
        uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: failure-screenshots-${{ matrix.project }}
          path: screenshots/
          retention-days: 7
```

#### Acceptance Criteria

- [ ]  Tests run automatically on every push to `main`
- [ ]  Pipeline uses GitHub Secrets (no exposed credentials)
- [ ]  HTML report is downloadable as an artifact from the Actions tab
- [ ]  Pipeline status badge is visible in README
- [ ]  Failed tests show screenshots in the uploaded artifacts

#### 🎤 Interview Questions — Phase 8

|#|Question|
|---|---|
|1|**How do you integrate Playwright tests into a CI/CD pipeline? Walk me through your workflow.**|
|2|**How do you handle environment-specific variables and secrets in CI/CD?**|
|3|**A test passes locally but fails in CI. What are the common causes and how would you debug it?**|

---

### 📌 PHASE 9: Advanced Patterns & Configuration

**Objective**: Implement production-grade patterns — parallel execution, retries, tagging, cross-browser testing, and environment-based configuration.

**Timeline**: Days 24–27 (4 days)

#### Tasks

|#|Task|What You Practice|
|---|---|---|
|9.1|Add test tags using `test.describe` titles or `@tag` annotations: `@smoke`, `@regression`, `@api`, `@visual`. Configure npm scripts: `test:smoke`, `test:regression`|Test categorization & filtering|
|9.2|Enable parallel execution: set `fullyParallel: true` and `workers: 4` in config. Ensure all tests are independent. Fix any that aren't|Parallel execution, test independence|
|9.3|Configure retry logic: `retries: 2` for CI, `retries: 0` for local. Use `process.env.CI` check|Flaky test management|
|9.4|Add cross-browser projects: Chromium, Firefox, WebKit + Mobile Safari, Mobile Chrome viewports|Cross-browser & responsive testing|
|9.5|Create `config/environments.js` — export configs for `dev`, `staging`, `production`. Use `cross-env` to select: `cross-env ENV=staging npx playwright test`|Multi-environment support|
|9.6|**New Test**: Complete end-to-end checkout: Search product → View details → Add to cart → Proceed to checkout → Fill payment info → Verify order placed → Delete account|Full E2E workflow|
|9.7|**New Test**: Navigate to Products → Click on category "Women > Dress" → Verify products are filtered correctly → Click on brand "Polo" → Verify brand products displayed|Category & brand filtering|
|9.8|**New Test**: On Product Detail page → Write a review (name, email, review text) → Verify "Thank you for your review" message|Form submission + success verification|
|9.9|Add `test.slow()` for known slow tests. Add `test.skip()` for environment-conditional tests. Add `test.fixme()` for known bugs|Test annotations|
|9.10|Create a global setup file that verifies the website is reachable before running tests (`globalSetup` in config)|Global setup/teardown|

#### Example: Test Tagging

JavaScript

```
const { test, expect } = require('../fixtures/pomFixtures');

test.describe('@smoke Login Tests', () => {
  test('Valid login shows user as logged in', async ({ loginPage, page }) => {
    // ...
  });
});

test.describe('@regression Cart Tests', () => {
  test('Add multiple products and verify cart totals', async ({ page }) => {
    // ...
  });
});
```

Bash

```
# Run only smoke tests
npx playwright test --grep @smoke

# Run everything EXCEPT visual tests
npx playwright test --grep-invert @visual
```

#### Example: `config/environments.js`

JavaScript

```
const environments = {
  production: {
    baseURL: 'https://automationexercise.com',
    apiURL: 'https://automationexercise.com/api',
  },
  staging: {
    baseURL: 'https://staging.automationexercise.com',
    apiURL: 'https://staging.automationexercise.com/api',
  },
};

function getEnvironment() {
  const env = process.env.ENV || 'production';
  return environments[env];
}

module.exports = { getEnvironment };
```

#### Updated `playwright.config.js` for Advanced Patterns

JavaScript

```
const { defineConfig, devices } = require('@playwright/test');
const { getEnvironment } = require('./config/environments');
require('dotenv').config();

const env = getEnvironment();

module.exports = defineConfig({
  testDir: './tests',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 4 : 2,
  
  reporter: [
    ['list'],
    ['html', { outputFolder: 'reports/html-report', open: 'never' }],
    ['allure-playwright', { outputFolder: 'reports/allure-results' }],
  ],

  use: {
    baseURL: env.baseURL,
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
    actionTimeout: 15000,
  },

  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
    {
      name: 'mobile-chrome',
      use: { ...devices['Pixel 5'] },
    },
    {
      name: 'mobile-safari',
      use: { ...devices['iPhone 13'] },
    },
  ],
});
```

#### Acceptance Criteria

- [ ]  `npx playwright test --grep @smoke` runs only smoke-tagged tests
- [ ]  Tests run in parallel without interfering with each other
- [ ]  Cross-browser tests pass on at least Chromium and Firefox
- [ ]  Environment can be switched via command line (`ENV=staging npm test`)
- [ ]  Retries are 2 in CI, 0 locally
- [ ]  At least 1 full E2E test covers the complete purchase workflow

#### 🎤 Interview Questions — Phase 9

|#|Question|
|---|---|
|1|**How do you handle parallel test execution? What problems can it cause and how do you prevent them?**|
|2|**What is your strategy for dealing with flaky tests? How do retries help and when can they hide real bugs?**|
|3|**How would you organize your test suite into smoke, regression, and sanity suites? What criteria determine which tests go where?**|

---

### 📌 PHASE 10: Interview Preparation — Refactor, Document & Polish

**Objective**: Transform the framework into a polished, portfolio-ready project. Write professional documentation. Prepare for interview discussions.

**Timeline**: Days 28–30 (3 days)

#### Tasks

|#|Task|What You Practice|
|---|---|---|
|10.1|**Code review your entire codebase**: consistent naming conventions, remove dead code, add JSDoc comments to all page object methods|Code quality|
|10.2|Add ESLint + Prettier configuration. Run `npx eslint . --fix`. Ensure zero lint errors|Professional tooling|
|10.3|Write a comprehensive `README.md` (see template below)|Documentation skills|
|10.4|Create `ARCHITECTURE.md` — a document explaining your framework's design decisions, folder structure, and patterns used|System design articulation|
|10.5|Record a 2-minute GIF/video of tests running in headed mode — embed in README|Visual portfolio impact|
|10.6|Ensure `.gitignore` is complete: `node_modules/`, `.env`, `reports/`, `test-results/`, `screenshots/`, `allure-results/`|Git hygiene|
|10.7|Final test run: `npx playwright test` — all tests green across Chromium + Firefox. Fix any failures|Stability verification|
|10.8|Add GitHub Topics to your repo: `playwright`, `automation`, `javascript`, `e2e-testing`, `page-object-model`, `sdet`|Discoverability|
|10.9|Review and practice answering all interview questions from every phase|Interview readiness|
|10.10|Write a `CONTRIBUTING.md` with instructions for adding new tests (shows you think about team collaboration)|Team-oriented mindset|

#### README.md Template

Markdown

```
# 🎭 Playwright E-Commerce Automation Framework

![Playwright Tests](https://github.com/YOUR_USERNAME/YOUR_REPO/actions/workflows/playwright.yml/badge.svg)

A production-grade, end-to-end test automation framework built with **Playwright** 
and **JavaScript** for the [Automation Exercise](https://automationexercise.com) 
e-commerce platform.

## 🏗️ Architecture

- **Page Object Model (POM)** with BasePage inheritance
- **Custom Playwright Fixtures** for dependency injection
- **Data-Driven Testing** with JSON, CSV, and Faker.js
- **API Testing** using Playwright's built-in `APIRequestContext`
- **Visual Regression Testing** with screenshot comparison
- **CI/CD** via GitHub Actions with cross-browser matrix
- **Multi-environment** support (dev / staging / production)

## 📂 Project Structure
```

(paste your folder tree here)

text

````

## 🚀 Getting Started

### Prerequisites
- Node.js >= 18
- npm >= 9

### Installation
```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git
cd YOUR_REPO
npm install
npx playwright install --with-deps
cp .env.example .env   # Edit with your credentials
````

### Running Tests

Bash

```
npm test                    # Run all tests
npm run test:headed         # Run in headed mode
npm run test:smoke          # Run smoke suite only
npm run test:chromium       # Run on Chromium only
ENV=staging npm test        # Run against staging
```

### Reports

Bash

```
npm run report:html         # Open HTML report
npm run report:allure-open  # Open Allure report
```

## 🧪 Test Coverage

|Module|Tests|Type|
|---|---|---|
|Authentication|6|UI|
|Products|5|UI|
|Cart|4|UI|
|Checkout|3|E2E|
|Contact Us|2|UI|
|Products API|4|API|
|User API|5|API|
|Hybrid|2|API + UI|
|Visual|4|Visual|

## 🛠️ Tech Stack

|Tool|Purpose|
|---|---|
|Playwright Test|Test runner & browser automation|
|Faker.js|Dynamic test data generation|
|dotenv|Environment variable management|
|Allure Reporter|Rich test reporting|
|GitHub Actions|CI/CD pipeline|
|ESLint + Prettier|Code quality & formatting|

## 📝 License

MIT

text

````

#### Acceptance Criteria
- [ ] README is professional with badges, table of contents, clear setup instructions
- [ ] `ARCHITECTURE.md` explains design decisions in your own words
- [ ] ESLint + Prettier configured with zero errors/warnings
- [ ] All tests pass in CI (green badge)
- [ ] You can articulate every framework design decision verbally
- [ ] Repository has at least 35+ test cases total

#### 🎤 Interview Questions — Phase 10

| # | Question |
|---|---|
| 1 | **Walk me through your automation framework architecture. Why did you make the design decisions you made?** |
| 2 | **If you were onboarding a new team member, how would they add a new test to your framework? How long should it take them?** |
| 3 | **What would you change or add to your framework if you had to scale it from 50 tests to 500 tests?** |

---

## 5. 📅 30-Day Calendar View

| Day | Phase | Key Milestone |
|-----|-------|---------------|
| 1 | Phase 1 | Project initialized, config done, first test passes |
| 2 | Phase 1 | Login tests (positive + negative) written |
| 3 | Phase 1 | Registration, contact, logout tests done |
| 4 | Phase 1 | All 8 linear tests passing, HTML report reviewed |
| 5 | Phase 2 | `BasePage.js` created, `LoginPage.js` + `HomePage.js` refactored |
| 6 | Phase 2 | Remaining page objects created |
| 7 | Phase 2 | Custom fixtures created, all tests refactored to POM |
| 8 | Phase 2 | Navbar test added, all POM tests passing, code reviewed |
| 9 | Phase 3 | Faker installed, `testDataGenerator.js` created, registration test uses Faker |
| 10 | Phase 3 | `loginData.json` created, login tests are data-driven |
| 11 | Phase 3 | `.env` configured, search tests parameterized, CSV test done |
| 12 | Phase 4 | `constants.js` created, `helpers.js` built, magic strings removed |
| 13 | Phase 4 | `auth.fixture.js` built, cart tests written (2 tests) |
| 14 | Phase 4 | Subscription test, hooks added, all helpers integrated |
| 15 | Phase 5 | HTML + Allure reporters configured, `test.step()` added |
| 16 | Phase 5 | Screenshot/video on failure working, trace viewer explored, npm scripts added |
| 17 | Phase 6 | `apiHelpers.js` created, first 4 API tests passing |
| 18 | Phase 6 | Remaining API tests (CRUD, negative) done |
| 19 | Phase 6 | Hybrid test built (API setup + UI verify + API cleanup) |
| 20 | Phase 7 | First 2 visual tests with baselines generated |
| 21 | Phase 7 | All 4 visual tests done, masking configured, snapshots committed |
| 22 | Phase 8 | GitHub Actions pipeline running tests on push |
| 23 | Phase 8 | Artifacts uploading, secrets configured, badge in README |
| 24 | Phase 9 | Tags added (@smoke, @regression), parallel execution enabled |
| 25 | Phase 9 | Cross-browser projects configured, environment config built |
| 26 | Phase 9 | Full E2E checkout test, category/brand filter tests, global setup |
| 27 | Phase 9 | All advanced patterns working, retries configured for CI |
| 28 | Phase 10 | Code review, ESLint + Prettier, dead code removed |
| 29 | Phase 10 | README + ARCHITECTURE.md written, .gitignore finalized |
| 30 | Phase 10 | Final full test run, all green, interview Q&A review |

---

## 6. 🎤 Complete Interview Question Bank (All Phases)

| Phase | Question | Concept Tested |
|-------|----------|----------------|
| 1 | What is the difference between `page.locator()` vs `page.getByRole()` vs `page.getByTestId()`? | Locator strategy |
| 1 | How does Playwright's auto-waiting work? How is it different from explicit waits in Selenium? | Waits |
| 1 | Explain `toBeVisible()` vs `toBeAttached()` vs `toBeEnabled()` | Assertions |
| 2 | What is POM and why is it critical for maintainable test automation? | Design patterns |
| 2 | How do Playwright fixtures compare to `beforeEach`? Why use fixtures for page objects? | Fixtures |
| 2 | How would you handle shared components (navbar, footer) across page objects? | Component architecture |
| 3 | What is data-driven testing? When would it be overkill? | Test design |
| 3 | How do you handle sensitive data (passwords, keys) in an automation framework? | Security |
| 3 | How would you run the same suite against different environments? | Configuration |
| 4 | Explain test isolation. How does Playwright achieve it? Where can it break? | Reliability |
| 4 | What is the difference between a fixture and a helper utility? | Architecture |
| 4 | How do you ensure tests can run in any order? | Independence |
| 5 | What is a Playwright Trace file and how do you debug with it? | Debugging |
| 5 | How would you set up reporting for stakeholders in CI? | Reporting strategy |
| 5 | What artifacts should be captured on test failure and why? | Diagnostics |
| 6 | How does Playwright's `APIRequestContext` differ from Axios/Postman? | API testing |
| 6 | What is a hybrid test? Give a real example. | Test strategy |
| 6 | How would you validate API response schema without a schema library? | Validation |
| 7 | What is visual regression testing? When is it most valuable? | Visual testing |
| 7 | How do you handle dynamic content in visual comparisons? | Practical challenges |
| 7 | What threshold would you set and how? | Configuration judgment |
| 8 | Walk me through your CI/CD pipeline for test automation. | DevOps |
| 8 | How do you handle secrets in CI/CD? | Security |
| 8 | A test passes locally but fails in CI. How do you debug? | Troubleshooting |
| 9 | How do you handle parallel execution? What problems can occur? | Scalability |
| 9 | What is your strategy for flaky tests? | Reliability |
| 9 | How do you organize smoke vs regression vs sanity suites? | Test strategy |
| 10 | Walk me through your framework architecture and design decisions. | System design |
| 10 | How would a new team member add a test? How long should it take? | Maintainability |
| 10 | How would you scale from 50 to 500 tests? | Scalability |

---

## 🏁 Quick Start Checklist

Run these commands on Day 1 to get started immediately:

```bash
# Create project directory
mkdir playwright-ecommerce-framework
cd playwright-ecommerce-framework

# Initialize Playwright (select JavaScript, tests folder, GitHub Actions YES)
npm init playwright@latest

# Install additional dependencies
npm install dotenv @faker-js/faker
npm install -D allure-playwright allure-commandline eslint prettier cross-env

# Create folder structure
mkdir -p pages tests/{auth,products,cart,checkout,contact,api,visual} test-data/testFiles utils config fixtures reports screenshots

# Create initial files
touch pages/BasePage.js pages/HomePage.js pages/LoginPage.js
touch utils/helpers.js utils/constants.js utils/testDataGenerator.js utils/apiHelpers.js
touch config/environments.js fixtures/pomFixtures.js
touch .env .env.example

# Add to .gitignore
echo "node_modules/" >> .gitignore
echo ".env" >> .gitignore
echo "reports/" >> .gitignore
echo "test-results/" >> .gitignore
echo "screenshots/" >> .gitignore
echo "allure-results/" >> .gitignore

# Verify setup
npx playwright test
````

