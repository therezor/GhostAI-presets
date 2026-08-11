---
name: creating-skills
description: How to write a skill for a GhostAI agent — the SKILL.md format, the frontmatter that decides when it fires, the agents field that scopes it, and the parser's constraints. Use whenever asked to create, write, author, add, edit, or fix a skill, a SKILL.md, or agent instructions, or when a skill exists but never triggers.
---

# Writing a skill

A skill is one folder under `skills/` holding a `SKILL.md`: YAML-ish frontmatter
(`name`, `description`, optional `agents`) and a Markdown body. The folder name is
the skill name.

```
skills/refactor/SKILL.md
```

## Skills load in three stages — design for it

| Stage | What loads | Cost | Implication |
|---|---|---|---|
| 1 | `name` + `description` | always in context | this alone decides whether the skill fires |
| 2 | the SKILL.md body | only when triggered | keep it under ~500 lines |
| 3 | bundled files (`references/`, `scripts/`) | on demand | effectively unlimited |

## The description is the product

Most skills fail by never triggering, not by giving bad instructions. The model
solves the task directly instead of consulting the skill. So write the
description to do two jobs at once — **what it does and when to use it** — because
by the time the body loads the decision is already made.

- Third person, plain language, one line.
- Name the concrete triggers: file extensions, tool names, verbs, synonyms the
  user might actually say.
- Be a little pushy: "Use whenever the user mentions X, Y or Z, even if they don't
  say 'skill'."
- Name the exclusion when a neighbour is easily confused ("...for a scanned PDF
  use read-attachment instead").
- **Put every "when to use" cue here, never in the body.**

Weak: `Builds dashboards for internal data.`
Strong: `Builds fast internal data dashboards. Use whenever the user mentions
dashboards, charts of company metrics, KPI views, or wants to display internal
data — even if they never say "dashboard". Not for one-off static charts.`

## Scoping: the `agents` field

An agent-scoped skill names its agents; a shared skill omits the field entirely.

Scoped — `skills/refactor/SKILL.md`:

```markdown
---
name: refactor
description: How this codebase wants a large refactor staged and reviewed.
agents: coder, ghost-runner
---
```

Shared — the field is simply absent (every agent sees it):

```markdown
---
name: code-review
description: The review checklist every agent follows before opening a PR.
---
```

Scope to the agent whose work the skill is about; leave it shared only when the
guidance genuinely cuts across all of them. A shared skill's description sits in
*every* agent's context, so shared is a cost, not a default.

## Three constraints the parser imposes

The frontmatter parser is hand-rolled (`packages/core/src/frontmatter.ts`), not a
YAML library. It changes what a valid sheet looks like:

- **`agents` is a comma-separated string on one line.** There is no list support.
  `agents: coder, ghost-runner` works; a block list silently vanishes, because
  `- coder` does not match the field regex and is skipped:

  ```markdown
  agents:          ← parses as the empty string
    - coder        ← ignored entirely — the skill ends up shared
  ```

- **An empty value means shared, so never leave a stray `agents:`.** A dangling
  colon parses to the empty string, which reads as *shared*, hiding a scoped sheet
  in plain sight. Omit the field or give it a value.

- **Quoting is optional and pointless.** One matching pair is stripped, so
  `agents: "coder, lead"` equals the bare form. The dotted form (`agents.list:`)
  is reachable but buys nothing and reads worse — don't.

## Writing the body

- **Imperative voice.** "Extract the text first, then chunk it" — not "you may
  wish to consider".
- **Give the reason.** A short why ("because `-c copy` cuts on a keyframe")
  generalises to cases you didn't foresee; a bare MUST doesn't.
- **Only write what isn't already known.** Environment quirks, exact flags, house
  conventions, output formats, the ordering that took three tries. Skip general
  knowledge — it dilutes the parts that matter.
- **Show a fixed output format literally**, don't paraphrase it:

  ```markdown
  ## Report structure — use exactly this
  # [Title]
  ## Summary
  ## Findings
  ```

- **Prefer an example to a description** for a fiddly rule.
- **Split when it grows past ~500 lines:** move the variable parts into
  `references/`, and leave a pointer saying *when* to read each one. Ship runnable
  helpers in `scripts/` rather than describing them in prose.

## Your loop

1. **Capture intent** — what should this let the agent do, what phrasings trigger
   it, what is the output, is it objectively checkable. If the workflow already
   happened in the conversation, mine it rather than asking from zero.
2. **Draft**, then reread with fresh eyes and cut.
3. **Test** on 2–3 realistic prompts — what a user would actually type, not "read
   file X". One-step prompts trigger nothing and prove nothing.
4. **Iterate by generalising, not patching.** The skill runs on thousands of
   prompts you will never see; special-casing your three tests makes it worse
   everywhere. If a rule is stubborn, reframe rather than piling on constraints.
5. **Deliver.** Keep the original `name` and folder when updating — never rename to
   `-v2`.

## Anti-patterns

- Trigger conditions buried in the body instead of the description.
- A description that names the topic but not the moment it applies.
- A wall of MUST/NEVER with no reasoning.
- Re-teaching what the model already knows.
- A 900-line SKILL.md that should be 150 lines plus three references.
- Rules that only make sense for the examples you tested on.
- A block-list `agents:` (silently ignored) or a stray `agents:` (silently shared).

## Checklist

- [ ] `name` is lowercase-hyphenated and matches the folder
- [ ] Description says both what it does and when to fire, with concrete triggers
- [ ] No "when to use" text stranded in the body
- [ ] `agents` is one comma-separated line, or absent for a shared skill — never a
      block list, never a dangling colon
- [ ] Body is imperative, reasoned, under ~500 lines
- [ ] Output formats shown literally; every reference file has a "read this when…"
      pointer
- [ ] Tested on 2–3 realistic prompts, and nothing in it patches one test case
