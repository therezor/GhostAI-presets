# The coding toolbox: read, run and modify code, offline.
#
# Built locally and referenced by image ID, never pulled at runtime — GhostAI
# has to come up on a machine with no internet, so the only network this image
# needs is the network that built it.
#
# Three runtimes (node, python3, rust), git, and the navigation pair (ripgrep,
# jq). That is enough to run tests, write scripts and make focused changes to a
# project mounted in the workspace, and to install what that project pins.
#
# **Rust is the expensive one**: rustc, cargo, LLVM and the gcc it links
# through take the image from about 150 MB to about 665 MB — more than three
# times everything else here put together. It is here anyway because a Rust
# project is unworkable without the toolchain: there is no `node -e` shortcut,
# nothing runs until it compiles, and a box that can read Rust but not build it
# can only guess whether a change is right.
#
# **This box has network access**, which most do not: its manifest's ceiling is
# `open`, because a coding box that cannot reach a registry cannot run a test
# suite whose dependencies are not already vendored. That is a real widening —
# node, python and git are arbitrary code execution, and egress turns that into
# a way out — so it is a per-toolbox decision recorded in the manifest, and
# changing it changes the hash and forces a re-approval.
#
# There is no scoped middle ground today: `allowlist` mode needs an egress
# gateway container to enforce it, and the runner refuses to start without one
# rather than quietly running wide open. Until that ships, the honest choice is
# between `none` and `open`.
FROM alpine:3.21

# One layer, no cache, versions unpinned on purpose: the image ID is the pin.
# `bash` because a toolbox lifts the exec guard's shell ban and a model
# reaching for a pipeline writes `bash -lc '…'`.
#
# `rust` drags in gcc, musl-dev and binutils as dependencies — rustc shells out
# to `cc` to link, so a Rust package without a C toolchain would install fine
# and fail on the first build. They are not listed here because apk resolves
# them; the probe below is what proves it. No `ca-certificates` either: cargo
# reaches crates.io through libcurl, and the bundle already in `alpine:3.21`
# (`ca-certificates-bundle`, at /etc/ssl/cert.pem) is the one it reads.
RUN apk add --no-cache \
      bash \
      git \
      nodejs \
      npm \
      python3 \
      rust \
      cargo \
      rustfmt \
      rust-clippy \
      ripgrep \
      jq \
      file \
      tini \
 && rm -rf /var/cache/apk/*

# Compile and link a real binary, because `cargo --version` does not.
#
# A missing linker is invisible to a version check and fatal to the first
# build, and it is the failure this image is most likely to acquire — it
# arrives by a dependency edge (`rust` -> `gcc`) rather than by anything
# written above, so an Alpine that stops declaring it would break the box
# silently. `--offline` keeps the assertion to the toolchain: no registry, no
# network, nothing to be flaky.
RUN set -eux; \
    cargo new --vcs none /tmp/probe; \
    cd /tmp/probe; \
    cargo build --offline; \
    ./target/debug/probe | grep -q Hello; \
    cargo clippy --offline --version >/dev/null; \
    cargo fmt --version >/dev/null; \
    rm -rf /tmp/probe /root/.cargo

# Commits made inside the box need an identity; a placeholder one keeps `git
# commit` working and makes clear in the log that a container authored it.
RUN git config --system user.name "GhostAI coder" \
 && git config --system user.email "coder@ghostai.local" \
 && git config --system --add safe.directory '*'

# A non-root user mapped to the host's uid:gid via the GHOST_UID/GHOST_GID build
# args — build.sh fills them from the workspace owner, and both default to 1000
# for a bare `docker build`. Matching the host user is what keeps anything the box
# writes into the mounted workspace owned by, and editable by, that user; a fixed
# uid was the most common source of root-owned / unwritable artefacts.
ARG GHOST_UID=1000
ARG GHOST_GID=1000
RUN set -eux; \
    if ! getent group "${GHOST_GID}" >/dev/null 2>&1; then addgroup -g "${GHOST_GID}" ghost; fi; \
    grp="$(getent group "${GHOST_GID}" | cut -d: -f1)"; \
    adduser -D -u "${GHOST_UID}" -G "${grp}" -h /home/ghost ghost

ENV HOME=/home/ghost

# Cargo's cache lives in the workspace, not in $HOME.
#
# The manifest runs this box with a read-only root and a 64 MB tmpfs over
# /home/ghost, so the default `$HOME/.cargo` would cap the crates.io index and
# every downloaded source at 64 MB of *memory* — charged against the container's
# 2 GB limit, and thrown away when the container exits, so each run would
# re-download what the last one fetched. `/workspace` is the bind mount: on
# disk, already writable by this uid, and persistent. A registry cache there is
# the same shape as the `node_modules` this box already writes beside the
# project, and it is what makes a second `cargo build` work with the network
# off.
#
# The cost is a `.cargo/` directory in the workspace — and, unlike
# `node_modules`, one no project's `.gitignore` lists, so it shows up untracked
# in `git status` and a careless `git add -A` would commit a few hundred MB of
# crate sources. The coder's skill sheets say not to; there is nothing in the
# image that can stop it.
ENV CARGO_HOME=/workspace/.cargo

USER ghost
WORKDIR /workspace
