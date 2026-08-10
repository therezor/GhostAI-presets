# Contributing

Want to share an agent? This repo is just JSON files. Add one, open a pull
request, done. Here's how, in plain steps.

## What you can add

- **A preset** — an agent definition. One JSON file. This is the common case.
- **A toolbox** — a sandboxed container of command-line tools that a preset can
  run in. Only needed if your agent has to run real programs (convert files,
  reach the network, and so on). A folder with a `Dockerfile` and a manifest.

Most contributions are just a preset.

## Create a preset

Make a file at `agents/<your-id>.json`. **The filename is the agent's id**, so
`agents/translator.json` creates an agent called `translator`.

The smallest useful preset is a name, a label, and what the agent should do:

```json
{
  "schema": "ghostai.agent-preset/1",
  "id": "translator",
  "label": "Translator",
  "systemPrompt": "# {{name}}\n\nYou are {{name}}. Translate whatever the user sends into clear, natural English, and nothing else. If it is already English, say so."
}
```

That's a complete, valid preset. A few things worth knowing:

- **`systemPrompt` is the agent's instructions.** Write it like you'd brief a
  new colleague. Two placeholders get filled in automatically:
  `{{name}}` (the name the operator gives the agent) and `{{workspaceId}}` (the
  folder it works in). Leave `systemPrompt` out entirely to inherit GhostAI's
  built-in prompt instead.
- **Tools are opt-in per agent.** If you don't mention `tools`, the agent gets
  the sensible defaults — it can read, write and edit files, remember things,
  and use skills, and it must ask before running a shell command. To change
  that, add a `tools` block mapping each tool to `"allow"`, `"ask"` or
  `"deny"`:

  ```json
  "tools": {
    "read_file": "allow",
    "list_dir": "allow",
    "write_file": "allow",
    "edit_file": "allow",
    "exec": "allow",
    "memory": "allow",
    "skill": "allow"
  }
  ```

- **A pure-chat agent** (no file access, no commands) sets
  `"toolsEnabled": false` and nothing else. See `agents/nano.json`.
- **Delegators** — an agent that hands work to other agents — list them under
  `subagents`, each with an `id` and a one-line description of when to use it.
  See `agents/team-lead.json`.

Full field list: `id`, `label`, `systemPrompt`, `toolsEnabled`, `tools`,
`toolbox`, `subagents`, plus the optional extra prompt slots (`livePrompt`,
`wrapUpPrompt`, and so on — most presets never touch these).

## Give a preset a toolbox (optional)

If your agent needs real command-line tools, point it at a toolbox by name:

```json
"toolbox": {
  "name": "coding",
  "network": { "mode": "none", "allow": [] }
}
```

`network.mode` is `"none"` (offline — the safe default), `"open"` (full
internet), or `"allowlist"` (only the IP ranges you list in `allow`). This is a
*request*: the toolbox itself sets the real ceiling, and the agent never gets
more than the toolbox allows.

## Create a toolbox (advanced)

Only if no existing toolbox fits. Add a folder `toolboxes/<name>/` with:

- **`Dockerfile`** — installs the command-line tools you want available.
- **`toolbox.json`** — the manifest: the list of tools (each with a short
  `use`, its `args`, and an `example`), plus the sandbox policy (memory, CPU,
  network ceiling, and so on). Copy an existing one like
  `toolboxes/data/toolbox.json` as your starting point and edit from there.

Build and try it locally:

```bash
./build.sh <name>          # builds the image and installs the manifest
ghostai toolbox approve <name>   # you review it, then approve it
```

Nothing runs until you approve it — approving records the exact bytes you read,
so an image can't change underneath you.

## Test it locally

```bash
ghostai agent install <your-id>
```

If your preset uses a toolbox, build and approve that first (above). Then start
a chat with the agent and make sure it behaves the way your prompt describes.

## Open a pull request

1. Fork this repo (`therezor/GhostAI-presets`).
2. Add your file(s) on a new branch.
3. Keep it small — one preset (or one toolbox) per pull request.
4. In the description, say what the agent is for and confirm you tested it with
   `ghostai agent install`.
5. Open the PR against `main`.

That's it. Thanks for contributing.
