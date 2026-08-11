---
name: sequence-staged-work
description: Run a task that takes more than one specialist by passing each one's output to the next. Use whenever a request needs several steps in order — find then convert then analyze, or research then compute — across different specialists. Covers ordering the stages and threading file paths between them so nothing is redone.
agents: lead
---

# Sequence stages, pass the output along

Some tasks are a pipeline: each specialist needs what the last produced. Run them
in order and thread the files through, rather than asking one specialist to do
everything.

- Order the stages by dependency — `researcher` gathers sources → `data-analyst`
  extracts the numbers → `coder` computes on them; or `data-analyst` finds and
  converts a document → `coder` parses the saved text.
- Pass **paths, not pasted contents**: tell stage two to read the file stage one
  wrote — all specialists share the same files.
- Check each stage's result before spending the next — a wrong extraction is
  cheaper to catch at stage one than after stage three has built on it.
- Merge the stages into one answer yourself, and keep attribution to which
  specialist and which file.
