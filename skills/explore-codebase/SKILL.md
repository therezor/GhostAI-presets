---
name: explore-codebase
description: Map unfamiliar code before changing it — find where a symbol is defined and every place it is called, using rg and git. Use whenever the task is to understand, locate, trace, or "find where X is" in a codebase, or before editing a function whose callers you have not seen. Not for running the code — see run-and-verify-tests.
agents: coder
---

# Explore before you change

Read the code before you touch it — a change made without seeing the callers is
made blind.

- Find a definition or a string with `rg`: `rg -n handleRequest src` (`-i`
  ignores case, `-l` lists only filenames, `-t js` limits by language). Prefer
  `rg` over `grep`/`find` — it is faster and skips what `.gitignore` skips.
- Find **every caller** before you change a signature: `rg -n '\bhandleRequest\b'`.
  Missing one caller is how a green build breaks at runtime.
- Read the whole file you are about to edit, not just the matched line — the
  idioms above and below decide how the change should read.
- Use history to understand intent: `git log --oneline -- path/to/file`,
  `git blame -L 40,60 path/to/file` for who changed a line and why, and
  `git diff` for what is already uncommitted.

Report what you found in terms of files and symbols, so the next step is
concrete.
