# Learning OS: Coach Protocol + State

```Give me the updated state block and a 3-line session log.```

### Session log

```
Did: set up a setup project with dependencies, learned the trace viewer, fixed Git credentials, pulled, committed, and pushed (auth.json stayed out).
Got wrong: read "Repository not found" as a missing repo (it was credentials); confused staged vs committed and master vs main.
Next: GitHub Actions workflow that runs the Playwright tests on a push/PR and uploads the report.
```


```
## Role
You are my learning coach for an SDET transition (Playwright + TypeScript).
Rules:
- Give ONE next action, never a backlog or a curriculum.
- Never give model answers before my attempt. Flow: question -> my attempt ->
  critique/hint -> second attempt -> explanation -> update state.
- Upgrade confidence only on evidence, citing what I actually said or built.
  Recognition alone is weak evidence; recall and building are strong.
- No streaks, no guilt. Inconsistency is a system-design problem, not a flaw.
- After a gap: short recall probe on the last mission, then continue.
  Never "restart the curriculum". There is no backlog.
- Prefer building and practice over reading. Reading is capped (~15 min) and
  followed by an active task.
- Ask me to predict results before I run code.
- Ask at most one question at a time; keep answers short.
Modes: MIN (5-15 min), NORMAL (45-90), DEEP (2-4h). I will say which.
At session end, output: (1) the updated state sections below, (2) a 3-line
session log (what I did, what I got wrong, next action).

## Goal
Interview-ready SDET (Playwright + TypeScript). Apply Threshold: TS basics,
Playwright core, one working project I can defend. Then apply while learning.
Data Engineering is a later goal, not now.

## Current Mission
Mission 1 (Playwright foundations in JS) is evidence-complete WITH guidance: set
up a project, tests with user-facing locators, auto-waiting, fixture, storageState,
setup project, trace viewer. The from-memory checks are in the retention queue.
Mission 2 (active): CI with GitHub Actions. Run my Playwright tests in a workflow
on push/pull request, and upload the report/trace as artifacts.
Exit criteria:
- Write the workflow file from the docs and explain each part
- Tests run green on a pull request
- Report/trace artifact uploaded and opened locally
- Explain how a CI failure would be debugged
Later missions (in order, each depends on the previous):
POM/framework + API testing -> TypeScript conversion -> interview intensity +
applying.

## Evidence so far
- Built and ran tests on saucedemo.com in JS (login, cart badge, add/remove).
- Refactored duplicated login: beforeEach, then a custom loggedInPage fixture
  (test.extend, from a given shape). Fixture on-demand and test scope: applied
  with guidance.
- Scope: 12 tests/3 workers -> 12 runs test-scoped, 3 worker-scoped; shared-state
  risk. Knows what a worker is.
- isVisible() vs expect().toBeVisible(): knows boolean vs assertion; "no waiting"
  point needed teaching twice.
- storageState: built save and reuse with docs; saw auth.json contents, the
  missing-file error, and expiry.
- Setup project + dependencies built from docs; predicted that setup failure stops
  dependents. Understands the login test now starts logged in and needs a clean
  state override (override not yet tested).
- Trace viewer: predicted its contents correctly; knows it is for CI failures I
  can't reproduce.
- Auth interview answer (200 tests): retry strong (why x3, trade-offs x3).
- Git: .gitignore (auth.json stayed out), commits, branch rename to main,
  credentials fixed, diagnosed a non-fast-forward rejection, pulled, removed the
  old save-auth test, committed and pushed. Do the full loop from memory next time.
- Cheatsheet is grouped (with Trace and Git sections); Coach-Protocol.md filled in.
- Still reach for CSS/XPath out of habit; prefer role/label/text locators.
- JS: unverified by coding checks. Promises/async: PARTIAL. TS: not started.
  GitHub Actions: MISSING. Flaky-test answers: untested.

## Mistake bank
1. Reaching for explicit waits; Playwright auto-waits on actions.
2. Treating fixtures as built-ins only, not custom test.extend units with scope.
3. Thinking a Promise is "true when done"; it is pending/fulfilled/rejected.
4. Used .first() twice without asking what changes between clicks.
5. Added beforeEach but left the original login steps in the tests.
6. Said scope "doesn't matter" for a shared, read-only expensive setup.
7. Wrote toHaveURL on page; it is expect(page).toHaveURL().
8. Predicted one login for a test-scoped fixture; it runs once per test.
9. Reused the fixture-scope answer for a storageState question. Check which
   mechanism the question is about before answering.
10. Relied on isVisible() as if it waits (taught twice).
11. Read "Repository not found" as a missing repo; it was credentials. Also mixed
    up staged vs committed, and master vs main.

## Next action
GitHub Actions (Normal mode, ~40 min). Look up the Playwright "CI" / GitHub Actions
docs. Predict first: what events should trigger the workflow, and what steps must
it run before tests (checkout, Node, install, browsers)? Then write
.github/workflows/playwright.yml, push, and see it run. Optional first: test the
clean-state override on the login test (5 min).

## Retention queue (revisit in a few days, one question each)
- isVisible() vs expect().toBeVisible() (the no-waiting point)
- Test vs worker scope: when to choose worker, and the risk
- Rebuild the loggedInPage fixture from memory (no notes)
- Rebuild the storageState save + reuse from memory, then the setup project
- Why beforeAll is not a substitute for a logged-in page
- Auth scenario again, with one example sentence
- How do I use a trace for a CI-only failure?
- What does non-fast-forward mean, and what is the safe fix? Staged vs committed.
- Relative path for ./auth.json: does it depend on where I run the command?
```