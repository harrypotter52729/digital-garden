
## Setup & running
- Import: `import { test, expect } from '@playwright/test'`
- Run one test file: `npx playwright test path/filename`
- Run headed: add `--headed`; one worker: add `--workers=1`
- VS Code auto-format: Shift + Alt + F
- Run commands from the project root (relative paths like `./auth.json` depend
  on where I run from; open question, verify in the docs)

## Trace viewer
- Capture: add `--trace on` to the command (or `trace: 'on-first-retry'` in config)
- Open the report: `npx playwright show-report`, click a test, open its trace
- Open a trace file: `npx playwright show-trace [file]`
- Shows before/after snapshots per action, highlighted locator, network, console,
  errors, timing.
- Why it matters: a CI failure I can't reproduce locally. CI records the trace,
  uploads it as an artifact, and I open it locally.

## Test structure
- `{ page }` goes in the test function parameters: `async ({ page }) => {}`.
  It is a built-in fixture.
- `test.describe('group name', ...)` groups tests.
- `test.beforeEach(...)` runs once before each test in its block; used for setup.
- Worker = a separate process that runs tests in parallel. Each has its own browser.

## Locators
- Prefer role, label, text, placeholder; CSS/XPath only as a last resort.
- If a locator matches many elements, `.first()` or `.nth(0)` works, but it is
  a last resort. Ask "what changes between clicks?" before using it twice.

## Assertions
- `await expect(page).toHaveURL(/inventory/)` (regex = partial match; call it on expect(page))
- `await expect(locator).toHaveText('1')` verifies text
- `expect(...).toBeVisible()` retries until it passes; `locator.isVisible()` checks
  once, does not wait, and returns true/false. Use expect to verify.

## Fixtures
- A fixture is a function with three parts: setup, `await use(...)`, teardown.
- Define with `base.extend({...})`. Built-in ones (`page`) and custom ones both exist.
- A test gets a fixture only if it lists it in its parameters (on-demand).
- Test-scoped by default: runs once per test that asks for it.
- Worker-scoped: runs once per worker process. Good for expensive, read-only
  setup. Risk: shared state between tests.
- Why a custom fixture instead of beforeAll?
  - beforeAll cannot use `page`/`context` (test-scoped), and needs a manual afterAll
  - beforeAll runs once per worker
  - a fixture gives control over scope, setup, and teardown, useful in parallel runs

## Auth (storageState)
- Save the session: `await context.storageState({ path: './auth.json' })`
  (save after asserting the login worked)
- `auth.json` holds cookies and localStorage. It is a login in plain text:
  never commit it (.gitignore).
- It expires. Missing file = every dependent test fails (a missing file error).
- Setup project (built): in `playwright.config`, a project with
  `testMatch: /.*\.setup\.js/`, and the test project lists it in `dependencies`.
  Setup runs first and creates `auth.json`. If setup fails, dependents don't run.
- Reuse the session in the dependent project: `use: { storageState: 'auth.json' }`
  (so no `test.use` needed in test files).
- Login test needs a clean state: `test.use({ storageState: { cookies: [], origins: [] } })`
  (not tested yet; verify in the docs).
- Trade-offs: expiry, shared user in parallel tests (shared cart), one setup
  failure fails all dependents, one file per role.

## Git
- `git status`: changed, staged, untracked files
- `git log --oneline`: commit history
- Staged is not committed; a push sends commits.
- `.gitignore`: `auth.json`, `node_modules`, `test-results`, `playwright-report`
- `git fetch` downloads without changing my files; `git pull` fetches and integrates.
- `non-fast-forward` rejection = the remote has commits I don't. Pull, then push.
  Never force-push to get around it.
- `Everything up-to-date` = I have nothing the remote lacks (not "I have everything").
- "Repository not found" can mean a credentials problem, not a missing repo.
- Rename branch: `git branch -M main`