# ghostai-presets

The things an operator installs into [GhostAI](https://github.com/therezor/GhostAI):
agent presets, and the toolboxes some of them run in. Data only — no
TypeScript, no build step, nothing imported.

```
agents/<id>.json           an agent preset. The filename is the agent id.
toolboxes/<name>/          a Dockerfile and the manifest describing its policy
skills/<name>/SKILL.md     one skill sheet, the procedure an agent reads
build.sh <name>            builds one image and installs its manifest
```

This repository is versioned and distributed independently of GhostAI itself,
so the presets and toolboxes can be updated on their own cadence. It is
published as the npm package **`@ghostwire/presets`**, which is how the CLI
finds `agents/` at runtime — in a global npm install and a local checkout
alike — by resolving `require.resolve('@ghostwire/presets/package.json')`
rather than any path relative to `dist/`.

Want to add your own agent? See [CONTRIBUTING.md](CONTRIBUTING.md) for a plain,
step-by-step guide and how to open a pull request.

## Presets

One JSON file per agent, all in one directory, whether or not the agent needs a
container. `ghostai agent install <id>` reads them from here, after looking in
`~/.ghostai/presets/` so an operator's own file of the same name wins.

A preset that needs a toolbox names it in `toolbox.name`; the toolbox itself is
installed and approved separately, and installing the preset refuses until it
is.

## Skills

One directory per sheet, each holding a `SKILL.md` — the procedure an agent
reads when it needs one, rather than something it carries in every prompt.

A preset names the sheets it wants, and `ghostai agent install <id>` copies
them into the workspace's `skills/`. They are the agent's to read, not the
operator's to approve: a sheet is text, and nothing in one grants a capability
the toolbox has not already allowed.

## Toolboxes

`build.sh <name>` runs `docker build`, pins the resulting image **id** into the
manifest and installs it to `~/.ghostai/toolboxes/<name>/`. Nothing runs until
`ghostai toolbox approve <name>` records the hash of the bytes you reviewed.

An image is referenced by its content-addressed **image id**, not a registry
tag, so an approved toolbox cannot change underneath the operator who approved
it — and so GhostAI runs on a machine with no internet.

### User and permissions

A toolbox runs as a non-root user whose **uid:gid is mapped to the operator's own**,
so anything a tool writes into the mounted workspace (`nmap -oN …`, `fetch --save …`)
stays owned by — and editable by — the host user. `build.sh` discovers this
automatically at build time: it takes the owner of `~/.ghostai/workspace` (falling
back to whoever runs the build), bakes it into the image via the `GHOST_UID` /
`GHOST_GID` build args, and writes it into the manifest's `user` field in place of
the `__HOST_USER__` placeholder. Nothing machine-specific is committed — the tracked
manifests carry the placeholder and the Dockerfiles default to `1000`.

Override the detection when GhostAI runs as a different account than the one building
(e.g. a dedicated NAS service user):

```
GHOSTAI_UID=1026 GHOSTAI_GID=100 ./build.sh recon
```

Files left over from an earlier build under a different uid keep their old
ownership; chown the workspace once to catch up:
`chown -R <uid>:<gid> ~/.ghostai/workspace`.
