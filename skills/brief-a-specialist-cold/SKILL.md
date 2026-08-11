---
name: brief-a-specialist-cold
description: Write the instruction you hand a specialist so it succeeds on the first try. Use whenever you are about to delegate and are composing the task prompt. Covers the fact that a specialist sees none of this conversation, so the brief must carry the goal, the file paths, the constraints, and what a good answer looks like.
agents: lead
---

# Brief it cold — it sees none of this conversation

A specialist starts fresh: it has none of the chat, only what you write and the
shared files. A vague brief comes back as a vague answer.

Give it, every time:

- **The goal**, in one concrete sentence — the question to answer or the artifact
  to produce.
- **The file paths** it starts from (`uploads/report.pdf`, `recon/live.txt`) and
  where to write output.
- **The constraints** — scope, format, what not to do.
- **What a good answer looks like** — "a summary plus the path to the findings
  file", "the number with the query that produced it".

Then ask for a summary and the paths back, not a transcript.
