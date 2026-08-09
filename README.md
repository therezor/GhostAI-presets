# ghostai-presets

The things an operator installs into [GhostAI](https://github.com/therezor/GhostAI):
agent presets, and the toolboxes some of them run in. Data only — no
TypeScript, no build step, nothing imported.

```
presets/<id>.json          an agent preset. The filename is the agent id.
toolboxes/<name>/          a Dockerfile and the manifest describing its policy
build.sh <name>            builds one image and installs its manifest
```

This repository is versioned and distributed independently of GhostAI itself,
so the presets and toolboxes can be updated on their own cadence. It is
published as the npm package **`@ghostbot/catalogue`**, which is how the CLI
finds `presets/` at runtime — in a global npm install and a local checkout
alike — by resolving `require.resolve('@ghostbot/catalogue/package.json')`
rather than any path relative to `dist/`.

## Presets

One JSON file per agent, all in one directory, whether or not the agent needs a
container. `ghost agent install <id>` reads them from here, after looking in
`~/.ghostai/presets/` so an operator's own file of the same name wins.

A preset that needs a toolbox names it in `toolbox.name`; the toolbox itself is
installed and approved separately, and installing the preset refuses until it
is.

## Toolboxes

`build.sh <name>` runs `docker build`, pins the resulting image **id** into the
manifest and installs it to `~/.ghostai/toolboxes/<name>/`. Nothing runs until
`ghost toolbox approve <name>` records the hash of the bytes you reviewed.

An image is referenced by its content-addressed **image id**, not a registry
tag, so an approved toolbox cannot change underneath the operator who approved
it — and so GhostAI runs on a machine with no internet.
