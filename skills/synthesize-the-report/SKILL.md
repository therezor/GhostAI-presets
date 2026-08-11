---
name: synthesize-the-report
description: Merge what the specialists found into one engagement report. Use whenever you are writing up an engagement or pulling several specialists' results together. Covers tying each confirmed finding to its reproduction and file, reconciling disagreements, and not relaying raw tool transcripts.
agents: ghost-runner
---

# One report, every finding tied to its proof

Merge findings into a single report — do not relay transcripts or paste tool
output.

- For each confirmed vulnerability: the type, the affected endpoint or parameter,
  the exact reproduction, and the impact — and the path to the `findings/` file it
  lives in.
- Reconcile or state disagreements plainly — a scanner flagged something the
  exploiter could not confirm, so say it is unconfirmed rather than dropping it
  silently.
- Keep attribution: which specialist found it, which file holds the detail.
- Order by severity so the reader sees what matters first. Stay within the
  authorized scope, and note anything you did not get to.
