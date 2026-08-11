---
name: run-and-verify-tests
description: Run a project's test suite before and after a change and report what actually ran. Use whenever the task involves tests — running them, fixing a failing test, checking a change did not break anything, or "make sure it still works". Covers finding the right runner and not overstating a green result.
agents: coder
---

# Run the tests before and after

Run the suite twice: **before** the change, so you know what was already broken
and do not get blamed for it, and **after**, so you know what you actually did.

- Find the runner before inventing one: read `scripts` in `package.json`
  (`jq .scripts package.json`), then run `npm test` or the specific
  `npm run <script>`. For one file, use the runner the project already uses
  (`node --test path`, `npx vitest run path`), not a different one.
- Python tests: `python3` here is standard library only, so `pytest` is not
  available unless the project vendors it — use `python3 -m unittest` for stdlib
  tests, and say so plainly if a project needs a runner that is not installed.
- **Report exactly what you ran and its result.** An unqualified "tests pass"
  that meant one file is how a red build reaches someone else — say "ran
  `npm test`, 42 passed" or "ran only `auth.test.js`".
- A test that was already failing before your change is not yours to hide — note
  it separately from anything you caused.
