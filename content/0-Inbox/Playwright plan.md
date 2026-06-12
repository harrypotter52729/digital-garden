# YOUR COMPLETE 90-DAY PLAN — ALL 7 SECTIONS

---

# ━━━ SECTION 1: QUIT-OR-STAY DECISION FRAMEWORK ━━━

## 1A. Financial Pre-Conditions (ALL must be TRUE before quitting)

|#|Condition|Minimum Threshold|Why This Number|
|---|---|---|---|
|1|**Liquid savings** (savings account, FD — NOT mutual funds/stocks)|**10 months** of total monthly expenses|India's current hiring cycle: 12–16 weeks to get offer + 0–90 day notice at new company. 10 months covers worst-case gap + 2 month buffer|
|2|**Active EMIs** (home loan, car loan, personal loan, education loan)|Either **zero** OR fully covered within the 10-month fund above|One missed EMI destroys CIBIL score, which you can't fix quickly|
|3|**Dependents** (parents, spouse without income, children)|Separate fund or independent income covering their needs for 6 months|Your learning capacity drops to zero when a parent needs medical money and you have no paycheck|
|4|**Health insurance**|Active independent policy — NOT employer-provided group cover|The day you resign, your group cover ends. One hospitalization can wipe out your savings|
|5|**Credit card debt**|**Zero** outstanding balance|36–42% annual interest on Indian credit cards will eat your savings alive|
|6|**Rent** (if applicable)|Included in the 10-month calculation + landlord accepts 2-month advance|Landlords in Bangalore/Hyderabad/Pune don't care about your career plans|

**Scoring: If even ONE condition is FALSE → do NOT quit.**

---

## 1B. Psychological Risk Assessment — Specific to YOUR Profile

|Risk Factor|Why It's Dangerous FOR YOU Specifically|Severity|
|---|---|---|
|**Inconsistency history + no external structure**|Your job forces you to wake up, log in, and function daily. Remove that, and your 3-on/7-off cycle becomes 1-on/14-off. You've seen this in yourself.|🔴 CRITICAL|
|**Introvert + isolation**|Full-time study at home = zero human interaction for weeks. For introverts, this doesn't energize — it creates a fog where days blur together and urgency evaporates.|🔴 CRITICAL|
|**India market slowdown guilt spiral**|You'll check LinkedIn/Naukri, see fewer postings, panic, lose motivation to study because "what's the point," then feel guilty for not studying. This cycle is lethal to progress.|🟡 HIGH|
|**Financial anxiety overriding learning**|Even with savings, watching your bank balance decrease monthly while not earning creates background anxiety that makes deep learning nearly impossible.|🟡 HIGH|
|**Resume gap perception**|Indian hiring managers in 2024–25 ARE asking about gaps. A 3–6 month gap with "I was upskilling" gets skepticism, not respect, especially for 5-year experienced candidates. They expect you to upskill while working.|🟡 HIGH|

---

## 1C. Third Options (Before the Binary)

|Option|How It Works|Feasibility for You|
|---|---|---|
|**Negotiate notice to 60 days**|Many Indian employers allow this via earned leave adjustment or manager approval. Ask HR: "Can I adjust my notice period using my earned leave balance?"|✅ HIGH — most companies allow this, you just haven't asked|
|**Take 2–3 weeks planned leave**|Accumulate earned leave, take a block off during Weeks 9–12 of the plan for intensive practice + interviews|✅ HIGH — you have 5 years of tenure, likely have leave balance|
|**Internal switch**|Ask if your company has projects using Playwright, Selenium, or Cypress. Even a lateral move gives you real tool exposure while staying employed|🟡 MEDIUM — depends on company size and projects|
|**Reduce work intensity**|If your current project is low-pressure, do the minimum at work and redirect energy to learning during non-peak hours|🟡 MEDIUM — depends on project demands|
|**Moonlight on weekends**|Take small freelance QA automation gigs on platforms like Upwork/Toptal to build real Playwright experience while employed|🔴 LOW — you have consistency issues; adding more commitments won't help|

---

## 1D. My Recommendation

**🟢 STAY IN YOUR JOB. DO NOT QUIT.**

**Reasoning:**

1. Your #1 problem is not time — it's consistency. Quitting gives you more time but does **nothing** for consistency. You'll have 14 hours/day of free time and use 0 of them.
2. The 12-week plan below requires **13.5 hours/week**. You have these hours while employed.
3. Your job provides: income, health insurance, routine, a reason to get up, and a safety net. These are prerequisites for learning, not obstacles to it.
4. India's market in 2024–25 favors employed candidates. Recruiters filter for "currently employed" on Naukri/LinkedIn.
5. **Your real action items**: (a) Check your earned leave balance tomorrow. (b) Plan a 2-week leave block around Week 10 of the plan. (c) Ask HR about notice period flexibility.

> [🔀 CHANGES IF YOU QUIT]: If you quit anyway, compress the 12-week plan to 8 weeks (you'll have ~35 hours/week). Move job applications to Week 4 instead of Week 7. But expect the inconsistency problem to get WORSE, not better.

---

---

# ━━━ SECTION 2: JAVASCRIPT FUNDAMENTALS — FAST-TRACK (Weeks 1–3) ━━━

## What You DO and DO NOT Need

|❌ You Do NOT Need|✅ You DO Need|
|---|---|
|DOM manipulation (document.querySelector)|async/await and Promises|
|Event listeners, callbacks in browser|Arrow functions|
|Node.js http server, Express|Classes (for Page Objects)|
|Webpack, Babel, bundlers|Modules (import/export)|
|Frontend frameworks (React, Vue)|Array methods (.map, .filter, .forEach, .find)|
|RegEx (beyond very basic)|Object destructuring|
|Prototypes, `this` deep-dive|Template literals|
|TypeScript|try/catch error handling|
||JSON.parse / JSON.stringify|
||String methods (.includes, .trim, .toLowerCase)|
||for...of loops|
||Ternary operator, spread operator|
||Optional chaining (?.)|

---

## Week-by-Week JS Breakdown with Playwright Exercises

### WEEK 1: Core Syntax (1.5 hrs/day weekdays, 3 hrs/day weekends = ~13.5 hrs)

|Concept|What to Understand|Playwright Mini-Exercise|Time|
|---|---|---|---|
|`const` vs `let`|When to use each. `const` for everything unless you need reassignment.|Declare `const baseURL = 'https://www.flipkart.com'` and `let pageTitle`. Write a test that assigns `pageTitle` after navigation and asserts it.|30 min|
|**Arrow functions**|`() => {}` syntax. How to write a function that takes parameters and returns a value.|Write a function `const getSearchURL = (query) => \`[https://www.flipkart.com/search?q=${query}\``](https://www.flipkart.com/search?q=$%7Bquery%7D%5C%60%60); use it in a Playwright test to navigate.|45 min|
|**Template literals**|Backtick strings with `${variable}` interpolation.|Write a test where the product name is a variable, and your XPath selector uses template literals: `` `//a[contains(text(), '${productName}')]` ``|30 min|
|**String methods**|`.includes()`, `.trim()`, `.toLowerCase()`, `.split()`|Write a test that gets a product title from the page using `textContent()`, trims whitespace, converts to lowercase, and asserts it `.includes('iphone')`|45 min|
|**Comparison & logic**|`===`, `!==`, `&&`, `\|`, ternary `? :`|Write a test that checks if a product price is above ₹10000. If yes, log "Premium product", else log "Budget product". Use ternary.|30 min|
|**Objects**|Creating objects, accessing properties with `.` and `[]`, nested objects|Create a `testData` object: `{ user: { name: 'Test', email: 'test@test.com' }, product: 'iPhone' }`. Use its properties in a test.|45 min|
|**if/else, for loop, for...of**|Basic control flow. `for...of` for iterating arrays.|Write a test that searches for a product, grabs all result titles into an array, loops through them with `for...of`, and logs each one.|1 hr|

**Weekend Project (3 hrs):** Combine everything into ONE test file: navigate to a site, search for a product using a variable, loop through results, check if any result includes the search term, assert at least one match.

**Resource:** Rahul Shetty's Playwright course — Sections on JavaScript Essentials (usually first 2–3 sections). Watch at 1.5x speed. If a concept is already clear from the table above, skip the video.

**Free backup resource:** [https://javascript.info](https://javascript.info/) — Chapters 2.1 to 2.15 ONLY (fundamentals). Do NOT go further.

---

### WEEK 2: Async + Arrays (The Playwright-Critical Week)

|Concept|What to Understand|Playwright Mini-Exercise|Time|
|---|---|---|---|
|**Promises (concept only)**|A Promise is a "future value." Understand `.then()` and `.catch()` conceptually. You will RARELY write raw Promises — but you must know what `await` is unwrapping.|Read [javascript.info/promise-basics](https://javascript.info/promise-basics). Write a simple Promise that resolves after 2 seconds. Log "done."|45 min|
|**async/await** ⭐|`async` makes a function return a Promise. `await` pauses until the Promise resolves. EVERY Playwright call is awaited.|Take any test you wrote in Week 1. Add proper `async` to the test function. Use `await` before every `page.goto()`, `page.click()`, `page.fill()`. Notice what breaks without `await`.|1.5 hrs|
|**await with multiple steps**|Sequential awaits. Understanding that `await` makes async code LOOK synchronous.|Write a test: navigate → search → wait for results → click first result → assert product page title. All with `await`.|1 hr|
|**Array basics**|`[]`, `.push()`, `.length`, accessing by index `[0]`, `[arr.length-1]`|Write a test that finds all product links on a page using `page.locator('.product-link').all()`. Store count. Assert count > 5.|45 min|
|**Array methods: .forEach()**|Loop through each element and do something.|Get all product titles. Use `.forEach()` to log each title with its index.|30 min|
|**Array methods: .filter()**|Return a new array with only elements that pass a condition.|Get all product prices as numbers. Filter to only those above ₹5000. Assert filtered array is not empty.|45 min|
|**Array methods: .map()**|Transform each element into something new.|Get all product title elements. Map them to their text content using `.textContent()`. Store as string array.|45 min|
|**Array methods: .find()**|Return the FIRST element matching a condition.|From your array of product titles, `.find()` the first one that `.includes('Samsung')`.|30 min|

**Weekend Project (3 hrs):** Write a test that:

1. Goes to a real e-commerce site
2. Searches for "laptop"
3. Collects all result titles and prices
4. Filters to laptops under ₹50,000
5. Asserts at least one exists
6. Logs the cheapest one

**Resource:** Rahul Shetty course — revisit the async/await section. Watch at 1x speed this time. This is the most important concept.

---

### WEEK 3: Classes + Modules + Error Handling (Framework Prep)

|Concept|What to Understand|Playwright Mini-Exercise|Time|
|---|---|---|---|
|**Classes**|`class`, `constructor`, methods. This IS Page Object Model in code.|Create a class `SearchPage` with constructor that takes `page`. Add method `async searchFor(term)` that fills the search box and clicks search.|1.5 hrs|
|**`this` keyword (basic)**|Inside a class, `this` refers to the current instance. `this.page` stores the page object.|In your `SearchPage` class, use `this.page` to call Playwright methods inside class methods.|30 min|
|**import/export**|`export class SearchPage {}` in one file. `import { SearchPage } from './SearchPage'` in another. ES modules.|Move `SearchPage` to its own file. Import it in your test file. Run the test.|1 hr|
|**Destructuring**|`const { page, browser } = ...` Extracting properties into variables.|In a Playwright test, use `const { page } = await browser.newContext().then(c => ({ page: c.newPage() }))` or the simpler fixture destructuring: `test('...', async ({ page }) => {})`.|45 min|
|**try/catch**|Wrapping code that might fail. `try { risky code } catch (error) { handle it }`|Write a test that tries to find an element that might not exist. Use try/catch. In catch, log a meaningful error and take a screenshot with `page.screenshot()`.|1 hr|
|**Spread operator** `...`|Merging objects: `const merged = { ...defaults, ...overrides }`|Create a default config object and an environment-specific override. Merge them using spread. Use the merged config in a test.|30 min|
|**Optional chaining** `?.`|Safe property access: `data?.user?.name` returns undefined instead of crashing.|Access a nested property from an API response or test data object using `?.`|30 min|

**Weekend Project (3 hrs):** Create your first mini-framework:

text

```
/tests/search.spec.js
/pages/SearchPage.js
/pages/ProductPage.js
/utils/testData.js
```

- `SearchPage` and `ProductPage` are classes with methods
- `testData.js` exports an object with test data
- `search.spec.js` imports both and runs a complete search → product flow

**Commit this to GitHub.** This is your first real framework commit.

**Resource:** Rahul Shetty course — POM section (preview it this week; deep-dive in Weeks 5–6). Also: [https://javascript.info/class](https://javascript.info/class) (just this one page).

---

---

# ━━━ SECTION 3: PLAYWRIGHT ROADMAP — 12-WEEK PLAN ━━━

## Honest Timeline Assessment

> **With 13.5 hours/week and your background, in 12 weeks you will be a confident MID-LEVEL Playwright automation engineer.** You will NOT be senior-level in framework architecture — that takes 6+ months of real project work. Target roles: "QA Automation Engineer" or "SDET" at mid-level (not "Lead" or "Senior Architect"). This is realistic and still gets you a good salary jump.

## Project Website Selection

Your portfolio project must be on a **real, publicly accessible website**. Choose ONE:

|Option|Website|Why It Works|Anti-Bot Risk|
|---|---|---|---|
|**A (Recommended)**|**[https://www.flipkart.com](https://www.flipkart.com/)**|Indian e-commerce, interviewers know it, complex UI (search, filters, product pages, cart)|🟡 Medium — stick to public pages, no login flows|
|**B (Backup)**|**[https://www.amazon.in](https://www.amazon.in/)**|Global brand, same complexity as Flipkart|🟡 Medium|
|**C (Safe fallback)**|**[https://github.com](https://github.com/)** (public pages only)|Zero anti-bot issues, has API too, meta (testing the platform your code lives on)|🟢 Low|

**Decision rule:** Start with Option A. If your tests get blocked consistently after 3 attempts with different approaches (user-agent spoofing, delays, headful mode), switch to Option C. Do not waste more than 1 day debugging anti-bot issues.

**For API testing (Weeks 11–12):** Use **[https://api.github.com](https://api.github.com/)** (public, free, well-documented, rate-limited but sufficient).

---

## The 12-Week Table

### 🟦 PHASE 1: FOUNDATION (Weeks 1–3) — JS + Playwright Revision

|Week|What to Learn|What to Build/Commit to GitHub|Interview Talking Point Unlocked|
|---|---|---|---|
|**Week 1**|JS core: variables, functions, arrow functions, template literals, strings, objects, loops (see Section 2)|**Repo: `playwright-automation-framework`**. Commit: `/exercises/js-basics/` folder with 7 mini-exercises from Section 2, each as a separate `.js` file|"I have strong fundamentals in JavaScript for test automation — I've worked with functions, template literals, and data-driven approaches."|
|**Week 2**|JS: async/await, Promises, array methods. **Revise Rahul Shetty course:** Sections on basic test writing, locators, actions, and assertions — watch at **1.5x–2x speed**. Pause only when something feels unfamiliar.|Commit: `/exercises/async-arrays/` folder + `/tests/basic/` folder with 5 revised basic Playwright tests on your chosen website (search, navigation, element verification)|"I understand asynchronous programming deeply — every Playwright interaction is asynchronous, and I handle sequential and parallel async operations confidently."|
|**Week 3**|JS: classes, modules, import/export, destructuring, try/catch, spread. **Revise Rahul Shetty:** Sections on handling web elements (dropdowns, frames, alerts, multiple tabs) — watch at **1.5x speed**|Commit: `/tests/elements/` with 5 tests covering: dropdown, checkbox, new tab, iframe (if applicable on your site), file upload. + first class-based file in `/pages/`|"I structure my code using classes and modules from day one. I don't write monolithic test files."|

**📌 Rahul Shetty Course Revision Guide for Weeks 1–3:**

|Course Section (approximate)|Action|Speed|
|---|---|---|
|JavaScript essentials|Skim. Skip if Week 1 exercises felt easy. Watch only if a concept was confusing.|2x|
|Playwright installation & setup|**Skip entirely**. You've done this.|—|
|Locator strategies (CSS, XPath, text, role)|**Watch** — you know XPath but may be weak on `getByRole`, `getByText`, `getByLabel` which interviewers ask about.|1.5x|
|Basic actions (click, fill, check, select)|**Skip** if your Week 2 tests worked. Watch only if you struggled.|—|
|Assertions (expect)|**Watch** — make sure you know `toHaveText`, `toHaveURL`, `toBeVisible`, `toHaveCount`, `toContainText`|1.5x|
|Handling dropdowns, checkboxes, radio buttons|**Watch** — quick revision.|1.5x|
|Handling frames, alerts, new windows/tabs|**Watch carefully** — these are common interview questions.|1x|
|File uploads, downloads|**Watch** if your chosen site has upload/download. Otherwise **skip**.|1.5x|

---

### 🟨 PHASE 2: BUILD (Weeks 4–6) — Complete Course + Real Project

|Week|What to Learn|What to Build/Commit to GitHub|Interview Talking Point Unlocked|
|---|---|---|---|
|**Week 4**|**Complete Rahul Shetty course:** Page Object Model section. This is your MOST IMPORTANT week for course material. Watch at 1x. Take notes in NotebookLM. Also read: [https://playwright.dev/docs/pom](https://playwright.dev/docs/pom)|Commit: `/pages/HomePage.js`, `/pages/SearchPage.js`, `/pages/ProductPage.js` — real POM classes for your chosen website. Refactor 3 existing tests to use these POM classes.|"I implement Page Object Model as a core design pattern. Each page is a class with locators as properties and actions as methods. Tests read like business workflows."|
|**Week 5**|**Complete Rahul Shetty course:** Remaining sections (likely 20–25% left — check your Udemy progress). Focus on any sections about: test configuration, fixtures, hooks, test organization. Also read: [https://playwright.dev/docs/test-fixtures](https://playwright.dev/docs/test-fixtures)|Commit: 2 more POM pages + `/tests/e2e/` folder with 5 end-to-end scenario tests using POM. Each test should be a complete user flow (e.g., "Search for product → filter by price → verify first result → check product details page").|"My tests are organized by user workflows, not by page. Each test validates a complete business scenario end-to-end."|
|**Week 6** 🟢 **CATCH-UP FRIENDLY**|If behind: Use this week to finish anything from Weeks 3–5. If on track: Write 5 more e2e tests, add `beforeEach`/`afterEach` hooks for common setup/teardown, add meaningful test descriptions. Read: [https://playwright.dev/docs/test-annotations](https://playwright.dev/docs/test-annotations)|Commit: Minimum 10 e2e tests total in repo. Add `test.describe()` blocks to group related tests. Add `test.skip()` and `test.fixme()` annotations where appropriate. Update README with project description and how to run tests.|"I use test annotations for maintainability — marking known issues as fixme, skipping environment-specific tests conditionally, and grouping tests by feature area."|

**📌 Rahul Shetty Course Completion Guide for Weeks 4–6:**

|Course Section|Priority|Action|
|---|---|---|
|Page Object Model|🔴 **CRITICAL**|Watch at 1x speed. Code along. Then RE-IMPLEMENT on your own project (not the course's example site).|
|Fixtures & test configuration|🔴 **CRITICAL**|Watch at 1x. This is where your framework becomes professional.|
|API testing|🟡 HIGH|Watch at 1x. But you'll deep-dive in Week 11. For now, understand the concept.|
|Visual testing / screenshot comparison|🟢 MEDIUM|Watch at 1.5x. You'll implement in Week 12.|
|CI/CD integration|🟡 HIGH|Watch at 1.5x. You'll implement in Week 10. Get the concept now.|
|Any "project" or "capstone" section|**Skip**|You're building your own real project. Don't copy the course project.|

---

### 🟧 PHASE 3: ARCHITECT (Weeks 7–9) — Framework Design

|Week|What to Learn|What to Build/Commit to GitHub|Interview Talking Point Unlocked|
|---|---|---|---|
|**Week 7**|**Framework config & environments**: `playwright.config.js` deep-dive. Multiple environments (dev/staging/prod URLs via `.env` files). Custom timeouts, retries, browser selection. **Read:** [https://playwright.dev/docs/test-configuration](https://playwright.dev/docs/test-configuration) + [https://playwright.dev/docs/test-use-options](https://playwright.dev/docs/test-use-options)|Commit: Advanced `playwright.config.js` with: multiple projects (chromium, firefox, webkit), `.env.staging` and `.env.prod` files, custom timeouts. Add `/utils/config.js` helper that reads environment variables.|"My framework supports multi-environment execution — same tests run against staging and production by switching a single environment variable. Configuration is centralized and environment-specific values are externalized."|
|**Week 8**|**Custom fixtures, test data management, utilities**: Create custom fixtures that set up test preconditions. External test data from JSON files. Utility functions (date formatters, random data generators). **Read:** [https://playwright.dev/docs/test-fixtures](https://playwright.dev/docs/test-fixtures) (advanced section)|Commit: `/fixtures/custom-fixtures.js`, `/test-data/products.json`, `/test-data/users.json`, `/utils/helpers.js`. Refactor at least 3 tests to use external test data (data-driven testing).|"I implement data-driven testing — test logic is separated from test data, which comes from external JSON files. This lets the team add new test cases without writing code."|
|**Week 9** 🟢 **CATCH-UP FRIENDLY**|**Parallel execution, retries, reporters**: Configure parallel test execution. Add retry logic for flaky tests. Integrate HTML reporter + explore custom reporter. **Read:** [https://playwright.dev/docs/test-parallel](https://playwright.dev/docs/test-parallel) + [https://playwright.dev/docs/test-reporters](https://playwright.dev/docs/test-reporters)|Commit: Update `playwright.config.js` with `workers`, `retries`, `reporter` settings. Add screenshots on failure (`screenshot: 'only-on-failure'`). Generate HTML report. Add a `/reports/` screenshot to README showing the report.|"My framework runs tests in parallel across 3 browser engines, with automatic retry for flaky tests and HTML reporting with failure screenshots. Execution time reduced from X minutes sequential to Y minutes parallel."|

---

### 🟥 PHASE 4: POLISH (Weeks 10–12) — CI/CD, API Testing, Portfolio

|Week|What to Learn|What to Build/Commit to GitHub|Interview Talking Point Unlocked|
|---|---|---|---|
|**Week 10**|**CI/CD with GitHub Actions**: Create a workflow that installs dependencies, runs tests, and uploads report as artifact. **Read:** [https://playwright.dev/docs/ci-intro](https://playwright.dev/docs/ci-intro) (GitHub Actions section)|Commit: `.github/workflows/playwright.yml`. Trigger: on push to main + manual trigger. Add the green CI badge to your README. Ensure tests pass in CI.|"My tests run automatically on every push to main via GitHub Actions. I've configured browser installation, dependency caching, and artifact upload for test reports in the pipeline."|
|**Week 11**|**API testing with Playwright**: `request` context for making API calls. Test API endpoints independently + mix API calls within UI tests (e.g., create test data via API, validate via UI). **Read:** [https://playwright.dev/docs/api-testing](https://playwright.dev/docs/api-testing)|Commit: `/tests/api/` folder with 5+ API tests against **[https://api.github.com](https://api.github.com/)** (public endpoints: repos, users, search). At least 1 mixed test: create something via API → verify via UI (e.g., search for a known GitHub repo via API, then verify it appears in UI search).|"I use Playwright for both UI and API testing within the same framework. I create test preconditions via API to make UI tests faster and more reliable — no more slow UI setup steps."|
|**Week 12**|**Visual testing + portfolio polish**: Screenshot comparison testing. Polish README (architecture diagram, setup instructions, tech stack, test results). Record a 2-minute demo video (optional). **Read:** [https://playwright.dev/docs/test-snapshots](https://playwright.dev/docs/test-snapshots)|Commit: `/tests/visual/` with 3 visual comparison tests. Final README with: project description, architecture diagram (text-based is fine), tech stack, setup steps, how to run, sample report screenshot, CI badge. Update all test descriptions to be professional.|"I've implemented visual regression testing to catch UI changes — any pixel-level difference is flagged with a diff image. Combined with CI/CD, this provides automated visual validation on every code push."|

---

### Architecture of Your Final GitHub Repository

text

```
playwright-automation-framework/
├── .github/
│   └── workflows/
│       └── playwright.yml
├── pages/
│   ├── HomePage.js
│   ├── SearchPage.js
│   ├── ProductPage.js
│   ├── CartPage.js
│   └── BasePage.js          ← (optional) shared methods
├── tests/
│   ├── e2e/
│   │   ├── search.spec.js
│   │   ├── productDetails.spec.js
│   │   ├── filtering.spec.js
│   │   └── navigation.spec.js
│   ├── api/
│   │   ├── users.api.spec.js
│   │   └── repos.api.spec.js
│   └── visual/
│       └── homepage.visual.spec.js
├── test-data/
│   ├── products.json
│   └── users.json
├── fixtures/
│   └── custom-fixtures.js
├── utils/
│   ├── helpers.js
│   └── config.js
├── playwright.config.js
├── .env.staging
├── .env.prod
├── package.json
├── README.md                 ← Professional, with badge, arch diagram, screenshots
└── .gitignore
```

---

---

# ━━━ SECTION 4: DESTROYING THE INCONSISTENCY CYCLE ━━━

## 4A. DIAGNOSIS: Why You Study 3 Days Then Stop for 7

Your pattern has a name. It's a combination of five identified psychological mechanisms. Understanding each one takes away its power.

|#|Mechanism|What Happens in Your Brain|How It Shows Up For You|
|---|---|---|---|
|1|**Novelty-Driven Dopamine Spike**|Starting a new plan releases dopamine. Your brain rewards the DECISION to change, not the actual change. By Day 3, the novelty is gone. Dopamine drops. The brain says "this isn't fun anymore."|Day 1 feels electric. Day 3 feels like grinding. Day 4 you "take a break" that becomes a week.|
|2|**The "What-The-Hell" Effect** (academic term: "abstinence violation effect")|Once you break a streak, perfectionism says the streak is ruined. Your brain then says "might as well wait for the next Monday/next month to restart clean."|You miss Day 4. By Day 5 you think "I'll restart next week properly." Next week becomes next month.|
|3|**Decision Fatigue Before Starting**|Every time you sit to study, you must decide: What topic? Which resource? Where did I leave off? This decision-making uses the SAME mental energy as studying. If you have to decide AND study, you often do neither.|You open your laptop, stare at Udemy, can't decide which video, open YouTube instead, feel guilty, close everything.|
|4|**Invisible Progress**|Learning doesn't have immediate visible outputs. You studied async/await for 2 hours but can't "see" that you learned it. Your brain can't justify the effort.|After 3 days, you feel like you haven't learned anything "real." Motivation craters.|
|5|**All-Or-Nothing Threshold**|You believe a "real" study session is 1.5–2 hours of focused work. If you only have 30 minutes, or feel tired, you skip entirely because "what's the point of 30 minutes."|Weekdays after work, you're tired. "I'll study properly on the weekend." Weekend comes, you "relax first" and lose the day.|

---

## 4B. SYSTEM DESIGN: The Anti-Inconsistency Machine

### The "Never Zero" Daily System

**Core rule: The MINIMUM viable day is 5 minutes. Five minutes counts as a full win. Your streak is protected.**

|Component|Exact Implementation|Why It Works|
|---|---|---|
|**Pre-decided daily task**|Every Sunday, spend 15 minutes writing exactly what you'll do each day of the upcoming week on a sticky note or phone note. Format: `Mon: Watch video 47 of Rahul Shetty course (timestamp 12:30). Tue: Write SearchPage.js class. Wed: ...`|Kills Decision Fatigue (mechanism #3). You never sit down wondering "what should I do."|
|**5-Minute Minimum**|If you do ANYTHING Playwright/JS-related for 5+ minutes, the day counts. Examples that count: reading 1 page of Playwright docs on your phone, watching 1 Udemy video at 2x speed on your phone during commute, reading your NotebookLM notes for 5 minutes, running your existing tests once and seeing them pass.|Kills All-Or-Nothing (mechanism #5). There's no "not enough time" excuse for 5 minutes.|
|**Phone-first start**|Begin each session by opening the Udemy app or NotebookLM on your PHONE, not your laptop. Read/watch for 5 minutes. If you feel like continuing, THEN open the laptop.|Near-zero activation energy. You're already on your phone anyway. This hijacks the scroll habit.|
|**Visual progress tracker**|Create a Google Sheet (or use a paper calendar). Each day: GREEN = 30+ minutes. YELLOW = 5–29 minutes. Leave blank for missed days. NEVER mark RED. There is no red. Only green, yellow, and blank.|Makes Progress Visible (mechanism #4). Seeing a row of green/yellow cells builds momentum visually.|
|**End-of-session bookmark**|At the end of EVERY session (even 5-minute ones), write one sentence in your phone notes: "Stopped at: [exactly where you are]. Next step: [exactly what to do next]."|Kills Decision Fatigue for the NEXT session. You can start instantly.|

### "What Counts" Examples (Anything Below = Yellow Day ✅)

|Activity|Time|Counts?|
|---|---|---|
|Read one page of Playwright docs on phone|5 min|✅ Yellow|
|Watch one Udemy video at 2x on phone|5–10 min|✅ Yellow|
|Open NotebookLM, read your last 3 notes|5 min|✅ Yellow|
|Run your existing tests, see them pass|3 min|✅ Yellow|
|Write ONE test (even incomplete)|10 min|✅ Yellow|
|Read a single section of your weekly plan|2 min|✅ Yellow|
|Think about Playwright while walking but do nothing|—|❌ No|
|Watch a Playwright YouTube video "for motivation" without taking notes|—|❌ No|

---

## 4C. RELAPSE PROTOCOL: When You Miss 2+ Days

> Print this. Screenshot it. Save it on your phone's home screen.

### Step-by-Step for the First 10 Minutes of Returning

|Step|Action|Time|
|---|---|---|
|1|Open your phone. Open NotebookLM or your notes. Read your last "Stopped at / Next step" bookmark.|2 min|
|2|Say out loud (yes, out loud, even if alone): **"I'm resuming, not restarting."**|10 sec|
|3|Do the smallest possible action from your bookmark. If it says "Next step: write ProductPage.js" — just CREATE the file and write the class name. Don't finish it. Just start it.|5 min|
|4|If that felt okay, continue for up to 15 more minutes. If not, stop. You've already won today. Mark it Yellow.|0–15 min|
|5|Write a NEW "Stopped at / Next step" bookmark.|1 min|
|6|Check your 12-week calendar (Section 7). Find where you ACTUALLY are. Adjust this week's plan if needed. Most likely, you're still within a catch-up week's range.|2 min|

### What NOT to Do After a Gap

|❌ DO NOT|✅ DO INSTEAD|
|---|---|
|Re-watch videos you already watched|Read your NotebookLM notes on those topics (faster)|
|Restart the course from the beginning|Go to where you left off in your weekly plan|
|Rewrite code you already wrote|Run your existing tests. See them pass. Remember that progress exists.|
|Set a new ambitious plan ("I'll do 3 hours today to make up")|Do 15 minutes. Just 15. That's your only goal.|
|Tell yourself "this time will be different"|Tell yourself "I'm resuming from where I was. The plan has buffer weeks."|

### Self-Talk Script (Say These Out Loud or Read Them)

> Copy these into a phone note titled "READ THIS WHEN YOU FEEL GUILTY":

1. **"Missing 7 days did not erase what I learned. My brain consolidated that knowledge while I was away. That's literally how memory works — spacing effect."**
    
2. **"I am on Week [X] of a 12-week plan that has built-in catch-up weeks. Missing days was EXPECTED when this plan was designed. I am still inside the plan."**
    
3. **"Three days of learning plus seven days off equals three days of learning. Three is more than zero. Imperfect progress crushes perfect paralysis."**
    
4. **"I don't need a fresh start. I need a next step. My next step is: [read your bookmark]."**
    
5. **"The person who wrote this plan (and I agreed to follow it) explicitly said I would fall off. This is the relapse protocol. I am following the plan by doing exactly this."**
    

---

---

# ━━━ SECTION 5: COMMUNICATION & INTERVIEW SKILLS FOR INTROVERTS ━━━

## 5A. Daily Speaking Practice (15 min/day, alone, no video calls)

### The "Teach Your Phone" Method

|Step|Action|Tool|
|---|---|---|
|1|Pick ONE concept from whatever you learned that day (or from the interview question list below).|Weekly plan|
|2|Open your phone's voice recorder (built-in app, no special tool needed).|Phone|
|3|Press record. Explain the concept as if teaching a junior QA engineer who has never heard of it. Speak for **2 minutes minimum**. Use this structure: "What it is → Why it matters → How I use it in my framework → Quick example."|Voice recorder|
|4|Stop recording. **Listen to yourself.** Note: Did you pause too long? Did you say "basically" or "actually" 10 times? Were you clear?|—|
|5|Record it **ONE more time** with improvements. Don't listen to the second one. Delete both recordings. The practice is in the doing, not the review.|—|
|6|Open NotebookLM. Write a 3-sentence summary of what you just explained. This locks it in memory.|NotebookLM|

**Total time: 12–15 minutes. No human interaction. No video. No pressure.**

**What to explain each day (rotate through these topics as you learn them):**

Week 1–3 topics: What is Playwright? | async/await | Arrow functions | Array methods in testing | Why classes for POM  
Week 4–6 topics: POM implementation | Fixtures | Locator strategies | Auto-waiting | Test hooks  
Week 7–9 topics: Framework architecture | Config management | Data-driven testing | Parallel execution | Custom reporters  
Week 10–12 topics: CI/CD pipeline | API testing approach | Visual testing | Debugging flaky tests | Your project architecture

---

## 5B. Top 15 Playwright/JS Interview Questions — India Mid-Level

### 🟢 EASY — Basics (Questions 1–5)

**Q1: "What is Playwright and how is it different from Selenium?"**

> **Template:** "Playwright is a modern end-to-end testing framework by Microsoft. The key differences from Selenium are: First, **auto-waiting** — Playwright automatically waits for elements to be actionable before performing operations, so you don't need explicit waits like in Selenium. Second, **built-in browser engines** — Playwright bundles Chromium, Firefox, and WebKit, so there's no driver management issue. Third, **single API for all browsers** — unlike Selenium where each browser needs a different driver setup. Fourth, **built-in API testing, visual testing, and trace viewer** — these require third-party libraries in Selenium. In my framework, I specifically benefit from [mention one: auto-waiting/trace viewer/parallel execution]."

**Q2: "What locator strategies does Playwright support? Which do you prefer and why?"**

> **Template:** "Playwright supports CSS selectors, XPath, text, and — what I prefer — **user-facing locators** like `getByRole()`, `getByText()`, `getByLabel()`, and `getByPlaceholder()`. I prefer `getByRole` because it mirrors how users interact with the page — finding a button by its role, a textbox by its label. This makes tests more resilient to HTML structure changes. That said, I use XPath when dealing with complex DOM structures where role-based locators aren't sufficient — for example, [give a specific scenario from your project like 'navigating through nested product listing elements where the hierarchy is 4 levels deep']."

**Q3: "Explain auto-waiting in Playwright."**

> **Template:** "Auto-waiting means Playwright automatically waits for elements to be ready before performing an action. Specifically, before a `click()`, Playwright waits for the element to be visible, stable (not animating), enabled, and not obscured by other elements. Before a `fill()`, it additionally waits for the element to be editable. This eliminates the need for `Thread.sleep()` or explicit waits that make Selenium tests flaky. In my framework, I've written [X] tests and I have exactly zero explicit `waitForTimeout()` calls because auto-waiting handles everything. The only exception is [mention if you had one — like 'waiting for a specific API response before proceeding, where I use `page.waitForResponse()`']."

**Q4: "How do you handle dropdowns and checkboxes in Playwright?"**

> **Template:** "For HTML `<select>` dropdowns, I use `page.selectOption()` which accepts value, label, or index — for example, `await page.selectOption('#sort', { label: 'Price: Low to High' })`. For custom dropdowns built with `<div>` elements, I click the dropdown trigger first, wait for the options to appear, then click the desired option using `getByText()` or a specific locator. For checkboxes, I use `page.check()` and `page.uncheck()` which are idempotent — `check()` does nothing if already checked, which prevents toggle issues. In my framework, I handle [mention a specific dropdown on your test site]."

**Q5: "What assertions does Playwright provide? Name the ones you use most."**

> **Template:** "Playwright uses the `expect` library with web-specific matchers. The ones I use most frequently are: `toBeVisible()` — to verify an element is rendered, `toHaveText()` — for exact text matching, `toContainText()` — for partial text matching, `toHaveURL()` — to verify navigation, `toHaveCount()` — to verify the number of elements in a list (I use this a lot for search results), and `toHaveAttribute()` — to verify element properties like href or src. All of these have built-in auto-retry — they poll until the condition is met or timeout occurs, which makes assertions non-flaky compared to Selenium's immediate assertions."

---

### 🟡 MEDIUM — Framework (Questions 6–10)

**Q6: "Explain Page Object Model. How do you implement it in Playwright?"**

> **Template:** "Page Object Model separates test logic from page interaction logic. Each page of the application has a corresponding class. The class contains locators as properties and user actions as methods. In Playwright, I implement it by creating a class that accepts `page` in its constructor and stores it as `this.page`. Locators are defined using `this.page.locator()` and methods are async functions that perform actions. For example, my `SearchPage` class has a `searchFor(term)` method that fills the search box and clicks the search button — the test just calls `await searchPage.searchFor('laptop')`. This gives me reusability — if the search box selector changes, I update ONE file, not 15 tests. In my framework, I have [X] page objects covering [list the pages]."

**Q7: "What are fixtures in Playwright? How are they different from beforeEach/afterEach?"**

> **Template:** "Fixtures are Playwright's dependency injection mechanism. While `beforeEach` runs setup code before every test, fixtures are more powerful because they provide isolated instances, support automatic teardown, and are composable. The built-in `page` fixture, for example, creates a fresh page for each test and closes it after. I create custom fixtures for common setup — for example, a `loggedInPage` fixture that handles authentication and provides a pre-logged-in page to the test. The test just destructures: `test('...', async ({ loggedInPage }) => {...})`. The key difference from `beforeEach` is that fixtures are lazy — they only run if the test actually needs them, and they handle teardown automatically."

**Q8: "How do you handle test data in your framework?"**

> **Template:** "I separate test data from test logic using three approaches depending on the context. For static data — like product names, URLs, expected values — I use external JSON files in a `/test-data/` directory and import them. For dynamic data — like timestamps or unique usernames — I have utility functions in `/utils/helpers.js`. For environment-specific data — like base URLs and credentials — I use `.env` files loaded via `dotenv` and accessed through a centralized config module. This means when we switch from staging to production, we change ONE environment variable, not 50 test files."

**Q9: "How do you run tests in parallel? How do you handle shared state?"**

> **Template:** "In Playwright, parallel execution is configured in `playwright.config.js` using the `workers` property. I set `workers: 4` for local execution and `workers: 2` for CI to balance speed with resource constraints. Each worker gets its own browser instance, so tests are naturally isolated. For shared state issues — like two tests trying to use the same test account — I use two strategies: first, unique test data per test (each test creates its own data via API setup); second, Playwright's `test.describe.serial()` for tests that must run in sequence. In my framework, parallel execution reduced test suite runtime from [X] minutes to [Y] minutes."

**Q10: "How do you generate and customize reports?"**

> **Template:** "Playwright has built-in reporters. I use three in combination: `html` reporter for detailed interactive reports with screenshots and traces — this is what I share with the team; `list` reporter for console output during development; and `junit` reporter for CI/CD integration since Jenkins/GitHub Actions parse JUnit XML natively. I configure these in `playwright.config.js` under the `reporter` property. For screenshots, I set `screenshot: 'only-on-failure'` so failed tests automatically capture the page state. I also enable `trace: 'on-first-retry'` which records a complete trace file for the first retry of a failed test — this includes DOM snapshots, network logs, and console messages, which makes debugging significantly faster."

---

### 🔴 HARD — Architecture/Debugging (Questions 11–15)

**Q11: "How do you handle authentication state across tests?"**

> **Template:** "Instead of logging in through the UI in every test — which is slow and flaky — I use Playwright's `storageState`. In a global setup file, I perform login ONCE, save the authentication cookies and localStorage to a JSON file using `page.context().storageState({ path: 'auth.json' })`. Then in `playwright.config.js`, I set `use: { storageState: 'auth.json' }` so every test starts pre-authenticated. This reduced my per-test overhead by [X] seconds. For tests that need different user roles — admin vs. regular user — I create separate storage state files: `admin-auth.json` and `user-auth.json`, and assign them to different test projects in the config."

**Q12: "How do you design your framework for scalability?"**

> **Template:** "My framework follows four principles for scalability. First, **layered architecture** — tests only interact with page objects, page objects interact with the browser, utilities handle cross-cutting concerns. No test file directly uses raw selectors. Second, **centralized configuration** — all environment-specific values, browser settings, and timeouts are in `playwright.config.js` and `.env` files, not hardcoded. Third, **shared base page** — common methods like `waitForPageLoad()` or `takeScreenshot()` live in a `BasePage` class that all page objects extend. Fourth, **data externalization** — test data is in JSON files, making it easy to add new test cases without code changes. This design means a new team member can add a test for a new page by creating one page object file and one test file, without understanding the entire framework."

**Q13: "How do you integrate Playwright with CI/CD?"**

> **Template:** "I use GitHub Actions with a dedicated workflow file. The pipeline triggers on every push to main and on pull requests. Steps: checkout code, setup Node.js, install dependencies with `npm ci`, install Playwright browsers with `npx playwright install --with-deps`, run tests with `npx playwright test`, and upload the HTML report as a GitHub artifact. Key configurations I've added: browser caching to speed up installs, retry logic in the workflow for infrastructure flakiness, and a Slack notification step for failures. The total pipeline runs in approximately [X] minutes. One challenge I faced was [mention a real one — like 'headless browser fonts rendering differently in CI vs local, which I fixed by installing OS font packages in the workflow']."

**Q14: "How do you debug a flaky test?"**

> **Template:** "I follow a systematic process. Step 1: Enable **Playwright Trace Viewer** with `trace: 'on'` and replay the failing test — this shows every action, network request, and DOM state. Step 2: Check if it's a **timing issue** — I look for actions on elements that might not be ready; even with auto-waiting, animations or lazy-loaded content can cause issues. Step 3: Check for **test isolation problems** — is this test depending on state from a previous test? I run it in isolation with `npx playwright test testname.spec.js`. Step 4: Check for **environmental factors** — different viewport sizes, network speed. I add `await page.waitForLoadState('networkidle')` if needed. Step 5: If the flakiness persists, I add `test.retry(2)` for that specific test and log a bug for the application team if it's a genuine UI instability."

**Q15: "How do you use Playwright for API testing?"**

> **Template:** "Playwright provides `request` context for API testing, so I don't need a separate tool like Postman or REST Assured. I create an API request context with `const apiContext = await request.newContext()`, then use `apiContext.get()`, `.post()`, `.put()`, `.delete()`. I parse responses with `response.json()` and assert using the same `expect` library. I use API testing in two ways: standalone API tests that validate backend endpoints independently (stored in `/tests/api/`), and as setup/teardown for UI tests — for example, creating a user via API before testing the UI login flow, which is faster and more reliable than creating the user through the UI. This hybrid approach reduced my test suite's total execution time by approximately [X]%."

---

## 5C. "I Know It But Can't Say It" — 3 Freeze-Breaking Techniques

### Technique 1: The Category-First Response

**When you freeze:** Start by categorizing the answer before giving details.

> **Filler phrase:** _"That's a good question — there are [two/three] aspects to this."_

Then list the categories. Even if you only know 2, the structure buys you time and makes your answer sound organized.

**Example:** Interviewer asks "How do you handle dynamic elements?"

- You freeze. You KNOW this but can't articulate it.
- Say: _"There are three aspects to handling dynamic elements in Playwright."_
- Now your brain has a structure to fill: (1) auto-waiting, (2) specific wait methods, (3) dynamic locators
- Even if you only deliver 2 of 3, you sounded structured.

### Technique 2: The Concrete Example Pivot

**When you freeze:** Pivot to a specific example from your project instead of explaining abstractly.

> **Filler phrase:** _"Let me walk you through how I handled this in my recent project."_

Then describe exactly what you did. Specific beats abstract. Interviewers often prefer "I did X" over "one should do X."

**Example:** Asked "Explain fixtures in Playwright"

- You freeze. The definition is foggy in your head.
- Say: _"Let me walk you through how I implemented this in my framework."_
- Describe: "I created a custom fixture called `authenticatedPage` that logs in once and provides a pre-authenticated page to all tests. Each test destructures it from the test function parameters."
- You just explained fixtures perfectly through an example.

### Technique 3: The Clarification Buy

**When you freeze:** Ask a clarifying question. This is NOT a sign of weakness — it's a sign of precision. It gives you 15–20 seconds to organize thoughts.

> **Filler phrases (pick one):**
> 
> - _"Just to make sure I address your specific concern — are you asking about [aspect A] or [aspect B]?"_
> - _"Are you asking about the implementation in Playwright specifically, or the general concept?"_
> - _"Do you want me to explain the concept first, or go directly to how I implemented it?"_

These are legitimate professional questions. They make you sound thorough, not incompetent. And they give your brain 15 seconds to find the answer.

### Bonus: Universal Thinking-Time Fillers (Memorize These 3)

|Phrase|When to Use|
|---|---|
|_"That's something I've implemented — let me structure my answer."_|When you need 5 seconds to think|
|_"In my experience with Playwright specifically..."_|When you need to narrow a broad question to your comfort zone|
|_"The way I approach this in my framework is..."_|When you know the practical answer but not the textbook definition|

---

---

# ━━━ SECTION 6: JOB SEARCH STRATEGY — INDIA SPECIFIC ━━━

## 6A. Handling "What's Your Notice Period?" — Scripts

### Scenario 1: Asked by Recruiter on Call (Screening Stage)

> **Script:** _"My contractual notice period is 90 days. However, I have [X] days of earned leave that I can use to reduce the effective joining timeline. With a confirmed offer letter, I'm confident I can negotiate an early release and join within 45–60 days. My current employer has accommodated early releases for colleagues in the past."_

**Key move:** Never say "90 days" alone. Always pair it with mitigation immediately.

### Scenario 2: Asked on Application Form (Dropdown)

- If options are: Immediate / 15 days / 30 days / 60 days / 90 days → **Select 60 days**
- In comments/additional info (if available), write: _"Contractual: 90 days. Negotiable to 60 with confirmed offer. Earned leave available for adjustment."_

### Scenario 3: Interviewer Uses It as a Rejection Reason

> **Script:** _"I understand 90 days seems long. In my experience, most candidates at my level in India have similar notice periods. What I can offer is flexibility — I can begin contributing to documentation, knowledge sessions, or remote onboarding activities during my notice period if that helps. I'm also willing to discuss a notice period buyout if your company supports that."_

### Scenario 4: When YOU Bring It Up (Proactive — Do This)

> In the first call with a recruiter, after they describe the role, say: _"Before we proceed, I want to be transparent about timeline. My current notice is 90 days, but I can realistically join in 55–65 days using my leave balance and early release negotiation. Is that workable for this role?"_

**Why proactive:** It filters out companies that genuinely can't wait, saving you interview time. Companies that proceed after hearing this will NOT reject you over it later.

---

## 6B. When to Start Applying

|Activity|Start Week|Why|
|---|---|---|
|**Update LinkedIn headline & summary**|Week 1|Passive — takes 20 minutes, makes you discoverable immediately|
|**Draft resume v1**|Week 4|By now you have JS skills + some Playwright project to mention|
|**Set up GitHub profile**|Week 5|Your repo has enough commits to not look empty|
|**Start applying (3–5 applications/week)**|Week 7|You can credibly discuss POM, framework design, and config management. Your project is substantive.|
|**Ramp up applications (8–10/week)**|Week 9|Your framework has CI/CD. You have API testing experience. You're interview-ready for mid-level.|
|**Maximum intensity**|Weeks 10–12|Portfolio is polished. Full confidence for interviews.|

> [🔀 CHANGES IF YOU QUIT]: Start applying at Week 5. You'll have more time for interview prep, and you need to move faster because the clock is ticking on your savings.

---

## 6C. Platforms That Work in India for QA Automation

|Platform|How to Use It Specifically|Expected Response Rate|
|---|---|---|
|**LinkedIn (primary)**|Set profile to "Open to Work" (visible to recruiters only). Headline: `QA Automation Engineer \| Playwright \| JavaScript \| 5 Years`. Post 1 short technical insight per week (e.g., "TIL: Playwright's `getByRole` locator is more resilient than CSS selectors because..."). Apply via "Easy Apply" but ALSO message the recruiter directly after applying: _"Hi [Name], I applied for [Role]. I have 5 years in QA automation and am currently building a Playwright framework — my project is here: [GitHub link]. Would love to discuss."_|8–12%|
|**Naukri**|Update resume. Set profile active. Keywords in profile: Playwright, JavaScript, Automation Framework, POM, CI/CD, API Testing. Respond to recruiter calls even if the role isn't perfect — they often have other openings.|5–8%|
|**Company career pages (direct)**|Target these companies specifically (known for QA automation hiring in India): **Flipkart, Myntra, Swiggy, PhonePe, Razorpay, Freshworks, Zoho, Browserstack, LambdaTest, BetterCloud, Thoughtworks, Mphasis, Epam, Nagarro, GlobalLogic, Publicis Sapient, Tiger Analytics**. Apply directly on their careers page.|3–5% but higher quality|
|**Instahyre**|Create profile. It's recruiter-initiated, so less effort from you. Good for product companies in India.|Passive — 3–5 messages/month|
|**Cutshort**|Similar to Instahyre. Better for startup/mid-size companies.|Passive|
|**Referrals (introvert-friendly method)**|You won't ask people on LinkedIn to refer you. Instead: Find the specific job posting → Find someone who works at that company on LinkedIn → Send this message: _"Hi [Name], I saw [Company] is hiring for [Role]. I have 5 years in QA automation and have been building a Playwright framework (GitHub: [link]). Would you be comfortable sharing my resume with the hiring team? I'd really appreciate it. Here's my resume: [link]. Thank you."_ Cold but direct. No small talk. No follow-up needed if they don't respond.|15–25% (referrals are the highest conversion)|

---

## 6D. Resume Bullet Points You Can Write TODAY

### Positioning Your Client-Tool Experience as a Strength

**Frame:** You automated hundreds of test scenarios across complex business workflows. The tool doesn't matter — the testing expertise does.

|Bullet Point|What It Demonstrates|
|---|---|
|"Designed and executed 500+ automated regression test scenarios across [X] business modules, reducing manual testing effort by 60% and enabling weekly release cycles"|Scale, impact, business value|
|"Built and maintained keyword-driven automation framework handling complex workflows including multi-step form submissions, dynamic data validation, and cross-module integration testing"|Framework thinking, complexity handling|
|"Identified and documented 200+ defects through automated test execution, collaborating with development teams to achieve 95% defect fix rate within sprint cycles"|Quality impact, collaboration|
|"Developed expertise in XPath-based element identification strategies for complex, dynamic web applications with nested frames and shadow DOM elements"|Technical depth — XPath is a transferable skill|
|"Currently building a Playwright-based automation framework with Page Object Model, CI/CD integration via GitHub Actions, and parallel cross-browser execution (GitHub: [link])"|Forward-looking, initiative, modern tools|

### Sample Skills Section

> **Automation Tools:** Playwright (JavaScript), [Client Tool Name] (Keyword-Driven Framework)  
> **Programming:** JavaScript (ES6+), XPath, CSS Selectors  
> **Framework Design:** Page Object Model, Data-Driven Testing, Custom Fixtures  
> **CI/CD:** GitHub Actions, Git version control  
> **Testing Types:** Functional, Regression, E2E, API, Visual Regression  
> **Other:** JIRA, Agile/Scrum, Test Planning, Defect Management

---

## 6E. GitHub Portfolio — Exact Setup

### Repository Structure

|Repo Name|Purpose|Minimum Commits|
|---|---|---|
|`playwright-automation-framework`|Your main project. THIS is what you link on your resume and LinkedIn.|4/week from Week 1 onward. Aim for 50+ total commits by Week 12.|
|Optional: `javascript-practice`|If you want to show JS fundamentals. Low priority.|Not required|

**You do NOT need multiple repos. One well-built repo > three half-done repos.**

### README Template for Your Main Repo

Markdown

````
# 🎭 Playwright E2E Automation Framework

Comprehensive end-to-end test automation framework built with
Playwright and JavaScript, testing [Flipkart.com / your chosen site].

## 🏗️ Architecture

├── pages/          # Page Object Model classes
├── tests/e2e/      # End-to-end UI tests
├── tests/api/      # API tests
├── tests/visual/   # Visual regression tests
├── test-data/      # External test data (JSON)
├── fixtures/       # Custom Playwright fixtures
├── utils/          # Helper functions and config
└── .github/        # CI/CD workflow

## ✅ Features

- Page Object Model design pattern
- Cross-browser testing (Chromium, Firefox, WebKit)
- Parallel test execution
- Data-driven testing with external JSON
- API testing with Playwright request context
- Visual regression testing
- CI/CD with GitHub Actions
- HTML reporting with failure screenshots
- Environment-based configuration (.env)

## 🚀 How to Run

```bash
npm install
npx playwright install
npx playwright test                  # Run all tests
npx playwright test --project=chromium  # Specific browser
npx playwright show-report           # View HTML report
````

## 📊 CI/CD Status

![Playwright Tests](https://github.com/%5Busername%5D/playwright-automation-framework/actions/workflows/playwright.yml/badge.svg)

## 📸 Test Report Sample

[Insert screenshot of your HTML report here]

## 🛠️ Tech Stack

- Playwright 1.x
- JavaScript (ES6+)
- Node.js
- GitHub Actions
- dotenv for config management

text

````

### GitHub Profile README

Create a special repo named exactly `[your-github-username]` with a `README.md`:

```markdown
## Hi, I'm [Your Name] 👋

**QA Automation Engineer** with 5 years of experience in test automation.
Currently building modern testing frameworks with **Playwright + JavaScript**.

🔭 Working on: [Playwright E2E Framework](link to your repo)
🌱 Learning: Advanced Playwright patterns, API testing, CI/CD
💬 Ask me about: Test automation, Page Object Model, XPath strategies

[![GitHub Streak](https://streak-stats.demolab.com/?user=yourusername)](https://git.io/streak-stats)
````

### Commit Strategy

|Day|Commit Type|Example Commit Message|
|---|---|---|
|Weekdays|Small focused commits|`feat: add SearchPage POM class with search and filter methods`|
|Weekends|Larger feature commits|`feat: implement data-driven testing with external JSON test data`|
|Any day|Even tiny changes count|`docs: update README with architecture diagram` or `refactor: extract common locators to BasePage`|

**Use conventional commit messages:** `feat:`, `fix:`, `test:`, `docs:`, `refactor:`, `ci:`

**Minimum: 3 commits/week. Target: 5/week.** Green contribution graph matters — recruiters check it.

---

## 6F. Positioning Client-Tool Experience

**Do NOT hide it. Reframe it.**

### In Resume:

> "5 years of test automation experience including end-to-end framework development using keyword-driven architecture (proprietary tooling) and modern JavaScript-based frameworks (Playwright). Deep expertise in XPath-based element strategies, complex business workflow automation, and regression suite management for enterprise applications."

### In Interviews:

> **When asked "What tools have you used?":**  
> _"In my current role, I work with a keyword-driven automation framework — think of it as a domain-specific abstraction over browser automation. I write structured commands to interact with web elements using XPaths, manage test flows, and handle assertions. This gave me strong fundamentals in test design, element identification, and thinking about automation architecture. I've now been building with Playwright to move to programmatic automation — here's my framework [show GitHub]. The transition was natural because the underlying concepts — locator strategies, wait mechanisms, test flow design — are the same. The difference is now I write JavaScript instead of keywords."_

### Why This Works:

1. You don't say "I only used a proprietary tool" — you say "keyword-driven automation framework"
2. You emphasize transferable skills: XPath, test design, flow management
3. You show initiative: "I've now been building with Playwright"
4. You demonstrate self-awareness about the industry shift
5. The GitHub link provides proof

---

---

# ━━━ SECTION 7: 90-DAY CALENDAR VIEW ━━━

> **This is your single source of truth. Print this or save it where you'll see it daily.**

| Week | Dates (fill in) | Learning Focus | Build / Commit to GitHub | Job Search Action | Communication Practice (15 min) | Milestone ✅ |



# ━━━ SECTION 7: 90-DAY CALENDAR VIEW (continued) ━━━

> **This is your single source of truth. Print this or save it where you'll see it daily.**

| Week          | Dates (fill in)    | Learning Focus                                                                                                                                                                                                             | Build / Commit to GitHub                                                                                                                                                                                                                 | Job Search Action                                                                                                                                                         | Communication Practice (15 min)                                                                                           | Milestone ✅                                                                              |
| ------------- | ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| **Week 1**    | ___/___ to ___/___ | JS Core: variables, arrow functions, template literals, strings, objects, loops                                                                                                                                            | Create repo `playwright-automation-framework`. Commit `/exercises/js-basics/` with 7 mini-exercise files. Min 4 commits.                                                                                                                 | Update LinkedIn headline to `QA Automation Engineer \| Playwright \| JavaScript \| 5 Years`. Set "Open to Work" (recruiters only).                                        | "Teach Your Phone" — Explain: What is Playwright? Why JS for automation? Record & re-record.                              | ✅ GitHub repo live. LinkedIn updated. JS basics comfortable.                             |
| **Week 2**    | ___/___ to ___/___ | JS: async/await, Promises, array methods (.forEach, .filter, .map, .find). **Revise** Rahul Shetty: basic tests, locators, actions, assertions at 1.5–2x speed.                                                            | Commit `/exercises/async-arrays/` + `/tests/basic/` with 5 basic Playwright tests on chosen website. Min 4 commits.                                                                                                                      | None — focus on learning.                                                                                                                                                 | Explain: async/await in Playwright. Why every Playwright call needs await.                                                | ✅ Can write async tests confidently. Array methods usable.                               |
| **Week 3**    | ___/___ to ___/___ | JS: classes, modules, import/export, destructuring, try/catch, spread, optional chaining. **Revise** Rahul Shetty: dropdowns, frames, alerts, tabs at 1.5x.                                                                | Commit `/tests/elements/` with 5 element-handling tests + first file in `/pages/` (a class-based page object). Weekend: mini-framework with `/pages/` + `/tests/` + `/utils/`. Min 4 commits.                                            | None — focus on learning.                                                                                                                                                 | Explain: What is a class in JS? How does it relate to POM?                                                                | ✅ First POM class written. Can use import/export. JS fundamentals DONE.                  |
| **Week 4**    | ___/___ to ___/___ | 🔴 **CRITICAL WEEK.** Rahul Shetty POM section at 1x speed. Read Playwright POM docs. Deep-dive into Page Object Model implementation.                                                                                     | Commit: `/pages/HomePage.js`, `/pages/SearchPage.js`, `/pages/ProductPage.js`. Refactor 3 existing tests to use POM classes. Min 4 commits.                                                                                              | Draft resume v1 using bullet points from Section 6D. Save as Google Doc, not PDF yet.                                                                                     | Explain: POM — what it is, why I use it, how I implemented it.                                                            | ✅ 3 POM classes built. 3 tests refactored. Resume v1 drafted.                            |
| **Week 5**    | ___/___ to ___/___ | Complete remaining 25% of Rahul Shetty course (config, fixtures, hooks, organization). Read Playwright fixtures docs.                                                                                                      | Commit: 2 more POM pages + `/tests/e2e/` folder with 5 end-to-end scenario tests using POM. Each test = complete user flow. Min 4 commits.                                                                                               | Set up GitHub profile README (Section 6E). Ensure repo has professional README (even if incomplete). Create Instahyre & Cutshort profiles.                                | Explain: Fixtures in Playwright — concept + your implementation.                                                          | ✅ Rahul Shetty course 100% done. 5 e2e tests. GitHub profile set up.                     |
| **Week 6** 🟢 | ___/___ to ___/___ | **CATCH-UP WEEK.** If behind: finish Weeks 3–5 overflow. If on track: add 5 more e2e tests, implement `beforeEach`/`afterEach` hooks, `test.describe()` blocks, annotations. Read Playwright annotations docs.             | Commit: Minimum 10 e2e tests total in repo. Add `test.describe()` grouping. Add `test.skip()` / `test.fixme()` where relevant. Update README with project description + run instructions. Min 3 commits.                                 | Finalize resume v2 — incorporate Playwright project. Get PDF ready. Upload to Naukri (set profile active, add keywords: Playwright, JavaScript, POM, CI/CD, API Testing). | Explain: Test hooks & annotations — when to use describe, skip, fixme.                                                    | ✅ 10+ e2e tests. Course complete. Naukri profile active. Solid mid-point.                |
| **Week 7**    | ___/___ to ___/___ | Framework config: `playwright.config.js` deep-dive — multiple projects/browsers, `.env` files for environments, custom timeouts, retries. Read Playwright config + use-options docs.                                       | Commit: Advanced `playwright.config.js` (3 browser projects, retry config). `.env.staging` + `.env.prod`. `/utils/config.js` environment helper. Min 4 commits.                                                                          | **START APPLYING: 3–5 applications/week.** LinkedIn Easy Apply + direct message recruiter. Apply on 2 company career pages from Section 6C list.                          | Explain: Multi-environment configuration — how same tests run on staging vs production.                                   | ✅ Framework is multi-browser, multi-environment. First job applications sent.            |
| **Week 8**    | ___/___ to ___/___ | Custom fixtures, test data management, utilities. External JSON test data. Utility functions (helpers). Data-driven testing approach. Read Playwright fixtures advanced docs.                                              | Commit: `/fixtures/custom-fixtures.js`, `/test-data/products.json`, `/test-data/users.json`, `/utils/helpers.js`. Refactor 3+ tests to use external test data. Min 4 commits.                                                            | 3–5 more applications. Send 3 referral request messages using the introvert-friendly script from Section 6C.                                                              | Explain: Data-driven testing — how you separate data from logic. Custom fixtures.                                         | ✅ Data-driven testing implemented. Custom fixtures working.                              |
| **Week 9** 🟢 | ___/___ to ___/___ | **CATCH-UP FRIENDLY.** Parallel execution, retries, reporters (HTML, list, junit). Screenshots on failure. Trace on first retry. Read Playwright parallel + reporters docs.                                                | Commit: Updated `playwright.config.js` with `workers`, `retries`, reporter settings, `screenshot: 'only-on-failure'`, `trace: 'on-first-retry'`. Generate HTML report. Add report screenshot to README. Min 3 commits.                   | **RAMP UP: 8–10 applications/week.** Mix of LinkedIn, Naukri, direct career pages, and referrals. Start responding to recruiter calls on Naukri.                          | Explain: Parallel execution strategy. How you debug flaky tests with Trace Viewer.                                        | ✅ Framework has parallel execution, reporting, auto-screenshots. Applying actively.      |
| **Week 10**   | ___/___ to ___/___ | CI/CD with GitHub Actions. Create workflow: install deps → install browsers → run tests → upload report artifact. Add CI badge to README. Read Playwright CI docs (GitHub Actions section).                                | Commit: `.github/workflows/playwright.yml`. Green CI badge on README. Ensure all tests pass in CI. Fix any CI-specific issues (headless rendering, timeouts). Min 4 commits.                                                             | 8–10 applications/week. If you have earned leave available, **apply for 2-week leave block starting Week 11 or 12** for intensive interview prep.                         | Explain: Your CI/CD pipeline — what triggers it, what it does, how artifacts work.                                        | ✅ CI/CD LIVE. Green badge on repo. This is a massive resume differentiator.              |
| **Week 11**   | ___/___ to ___/___ | API testing with Playwright `request` context. Standalone API tests + hybrid tests (API setup → UI validation). Use `https://api.github.com` public endpoints. Read Playwright API testing docs.                           | Commit: `/tests/api/` folder with 5+ API tests (GET users, GET repos, search). At least 1 hybrid test: verify via API → confirm via UI. Min 4 commits.                                                                                   | 8–10 applications/week. Start scheduling interviews. Practice answering Q11–Q15 (hard questions from Section 5B) daily.                                                   | Explain: API + UI hybrid testing approach. Why API for setup. How `request` context works.                                | ✅ API testing added. Framework covers UI + API + Visual (next week). Nearly complete.    |
| **Week 12**   | ___/___ to ___/___ | Visual testing (screenshot comparison). Final portfolio polish: README with architecture diagram, tech stack, setup steps, report screenshot, CI badge. Optional: record 2-min demo video. Read Playwright snapshots docs. | Commit: `/tests/visual/` with 3 visual comparison tests. **Final README polish** — professional, complete, impressive. Clean up any messy code. Add JSDoc comments to POM classes. Ensure all tests pass locally + in CI. Min 4 commits. | **MAXIMUM INTENSITY.** 10+ applications/week. Interview daily if possible. You are at peak readiness.                                                                     | Full mock interview: pick 5 random questions from Section 5B, answer into voice recorder, listen back, critique yourself. | ✅ FRAMEWORK COMPLETE. Portfolio polished. Interview-ready. This is your graduation week. |

---

## Weekly Time Budget Breakdown

| Day | Learning | Building/Coding | Job Search | Speaking Practice | Total |
|-----|----------|----------------|------------|-------------------|-------|
| **Mon** | 45 min | 30 min | 0 | 15 min | **1.5 hrs** |
| **Tue** | 30 min | 45 min | 0 | 15 min | **1.5 hrs** |
| **Wed** | 45 min | 30 min | 15 min | 0 | **1.5 hrs** |
| **Thu** | 30 min | 45 min | 0 | 15 min | **1.5 hrs** |
| **Fri** | 30 min | 30 min | 15 min | 15 min | **1.5 hrs** |
| **Sat** | 1 hr | 1.5 hrs | 15 min | 15 min | **3 hrs** |
| **Sun** | 1 hr | 1.5 hrs | 15 min | 15 min | **3 hrs** |
| **TOTAL** | **5 hrs** | **5.5 hrs** | **1 hr** | **1.5 hrs** | **13 hrs/week** |

> [🔀 CHANGES IF YOU QUIT]: Double all coding/building time. Add a morning routine block (9 AM–12 PM = learning, 2 PM–5 PM = building). Keep job search and speaking practice times the same. Total becomes ~28 hrs/week. But remember: more time ≠ more consistency. Your 5-minute minimum rule and relapse protocol matter MORE than extra hours.

---

## Key Checkpoints — "Am I On Track?"

| After Week | You Should Be Able To... | If You Can't... |
|------------|--------------------------|-----------------|
| **3** | Write a Playwright test with async/await, use array methods, create a basic class, import/export between files | You rushed. Go back to Week 2 exercises. Week 6 is your catch-up. Don't panic. |
| **6** | Explain POM, have 10+ working e2e tests on a real website, navigate Playwright docs independently | You're behind but recoverable. Use Week 6 fully for catch-up. Delay job applications to Week 9. |
| **9** | Discuss framework architecture in an interview: config, data-driven testing, parallel execution, reporting. Your GitHub repo looks professional. | You're significantly behind. Focus Weeks 10–12 on getting the repo to Week 9 quality. Apply anyway — you're stronger than you think. |
| **12** | Confidently answer 12 of the 15 interview questions from Section 5B. Your repo has 40+ commits, CI/CD green badge, clean README. | You did the work but don't feel ready. That's normal. Imposter syndrome is not incompetence. Apply aggressively. You ARE ready for mid-level roles. |

---

## The "Bare Minimum" Version (If Everything Goes Wrong)

> **If you only complete 60% of this plan, here's what you MUST have by Week 12:**

| Priority | Non-Negotiable Deliverable |
|----------|---------------------------|
| 🔴 P0 | GitHub repo with 8+ working POM-based tests on a real website |
| 🔴 P0 | `playwright.config.js` with at least 2 browser projects |
| 🔴 P0 | Clean README with project description, how to run, and tech stack |
| 🔴 P0 | Can answer interview questions Q1–Q8 from Section 5B confidently |
| 🔴 P0 | Resume updated with Playwright project listed |
| 🟡 P1 | CI/CD with GitHub Actions (even a basic workflow) |
| 🟡 P1 | External test data (at least 1 JSON file) |
| 🟡 P1 | Can answer Q9–Q12 |
| 🟢 P2 | API tests |
| 🟢 P2 | Visual tests |
| 🟢 P2 | Can answer Q13–Q15 |

**Even the P0-only version gets you interviews for mid-level QA automation roles in India. P1 makes you competitive. P2 makes you stand out.**

---

## Final Reality Check

| Question | Honest Answer |
|----------|---------------|
| Will 90 days make me a senior automation architect? | **No.** It makes you a confident, portfolio-backed mid-level Playwright engineer. Senior takes 12–18 months of real project work. |
| Will I get a job within 90 days? | **Maybe.** India's market in 2024–25 means 90 days of applying yields 5–15 interview calls if your resume + GitHub are solid. Offer timelines: 2–6 weeks from first call. Realistic total: 4–6 months from today to joining a new company. |
| What salary increase can I expect? | **20–40%** if moving from a service company with proprietary tools to a product/good-service company with Playwright. Higher if your current salary is significantly under market due to the proprietary tool ceiling. |
| What if I fall off the plan for 2 weeks? | **Re-read Section 4C.** You're resuming, not restarting. Check which catch-up week (6 or 9) is closest. Adjust. The plan survives a 2-week gap. It does NOT survive quitting the plan entirely. |
| What if I follow the plan perfectly? | **You won't.** Nobody does. The plan is designed for 70–80% completion. That's enough. Stop waiting for perfection. Start Week 1 today. Not Monday. Today. |

---

## Your Exact Next 3 Actions (Do These Within 24 Hours)

| #   | Action                                                                                                                                                                          | Time   | Tool        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ | ----------- |
| 1   | Create the GitHub repo: `playwright-automation-framework`. Add a basic README with just the project title and "Work in progress." Make ONE commit. Your streak starts now.      | 10 min | GitHub      |
| 2   | Open your phone. Create a note titled **"STOPPED AT / NEXT STEP"**. Write: `"Stopped at: Created GitHub repo. Next step: Week 1 Day 1 — const vs let exercise from Section 2."` | 2 min  | Phone Notes |
| 3   | Open your calendar app. Block 1.5 hours for tomorrow. Title: `"Playwright — Week 1 Day 1"`. Set a reminder 15 minutes before.                                                   | 3 min  | Calendar    |

**Total: 15 minutes. Zero excuses. Go.**