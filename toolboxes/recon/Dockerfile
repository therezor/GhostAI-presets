# The recon toolbox: map a target — subdomain enumeration, DNS, live-host
# probing, port and service scanning, crawling, and TLS/OSINT — from one box
# that reaches the target directly.
#
# This box and `web-research` were one image once, on the theory that a recon
# agent who fingerprints a host and a researcher who reads a page start from the
# same handful of tools (curl, openssl, python3, jq, rg). They do — but the
# middle they shared was small and the ends were not: recon carries static Go
# scanners and a raw-socket network layer, research carries an OCR stack and a
# Python web-extraction stack, and neither half ever ran the other's heavy part.
# Merged, every recon session paid for a hundred megabytes of OCR data it never
# loaded. Split, this box carries only what a recon agent actually runs.
#
# Built locally and referenced by image ID, never pulled at runtime — GhostAI has
# to come up on a machine with no internet, so the only network this image needs
# is the network that built it.
#
# Alpine rather than Debian because the whole point is that this is cheap to
# start: a session pays one container start. The static Go scanners are the
# heaviest thing in it, and they are single binaries with no runtime dependencies.
#
# What is deliberately *absent* is a headless browser. Chromium would multiply
# the image and hand the sandbox a JavaScript engine with a network stack, which
# is a large attack surface to carry for the small number of pages that need it.
# `katana` crawls without JavaScript and handles the rest.

# --- build stage: compile the Go recon tools, fully static -------------------
# `golang:alpine` unpinned on purpose, like every base here: the built image's
# id is the pin, and the ProjectDiscovery tools track a fast-moving Go minimum,
# so floating to the latest Go keeps them building rather than pinning to a
# version that goes stale under them.
FROM golang:alpine AS build

# CGO off so every binary is static and depends on nothing in the final image.
# GOTOOLCHAIN=auto so a tool that requires a newer Go than the image ships pulls
# that toolchain itself rather than failing — this stage has network.
# GOSUMDB=off so a flaky sum.golang.org lookup does not fail the build; modules
# still come through the proxy and the image is pinned by its id regardless.
ENV CGO_ENABLED=0 GOFLAGS=-buildvcs=false GOTOOLCHAIN=auto GOSUMDB=off

# Each is a single command the manifest names. `@latest` rather than a pin for
# the same reason the base image is unpinned: the built image's id is the pin,
# not the source ref it was built from.
RUN go install github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest \
 && go install github.com/projectdiscovery/dnsx/cmd/dnsx@latest \
 && go install github.com/projectdiscovery/httpx/cmd/httpx@latest \
 && go install github.com/projectdiscovery/katana/cmd/katana@latest \
 && go install github.com/projectdiscovery/tlsx/cmd/tlsx@latest \
 && go install github.com/lc/gau/v2/cmd/gau@latest \
 && go install github.com/tomnomnom/waybackurls@latest \
 && go install github.com/tomnomnom/gf@latest \
 && go install github.com/tomnomnom/anew@latest \
 && go install github.com/owasp-amass/amass/v4/...@master

# --- runtime stage -----------------------------------------------------------
FROM alpine:3.21

# One layer, no cache, and versions unpinned on purpose: the image ID is the
# pin. A toolbox references the built result by content hash, so what matters is
# that a given image is immutable — not that rebuilding it reproduces byte for
# byte, which alpine's rolling repos cannot promise anyway.
# `bash` is here for one reason: a toolbox lifts the exec guard's shell ban, so
# a model reaching for a pipeline writes `bash -lc '…'` — which is what it has
# seen everywhere — and busybox `ash` alone answers "bash: not found". One
# megabyte to avoid a failure mode that reads as the toolbox being broken.
#
# `python3` with `py3-requests` is the glue between scanners — a custom request
# or a bit of parsing the other tools cannot express — not a web-extraction
# stack: the OCR and DOM-scoring machinery lives in the `web-research` box now.
RUN apk add --no-cache \
      bash \
      ca-certificates \
      curl \
      jq \
      openssl \
      ripgrep \
      python3 \
      py3-requests \
      tini \
 && rm -rf /var/cache/apk/*

# The recon and network layer. `nmap-scripts` is the NSE library `--script vuln`
# needs; `libpcap` is what the raw-socket scanners link against; `bind-tools`
# supplies dig/host/nslookup. These reach the target directly, which is why this
# box's manifest caps network at `open` and adds back `NET_RAW` for `nmap -sS`.
RUN apk add --no-cache \
      nmap \
      nmap-scripts \
      masscan \
      libpcap \
      bind-tools \
      whois \
      sslscan \
      wget \
      git \
 && rm -rf /var/cache/apk/*

# The compiled scanners, dropped where the rest of the PATH already looks.
COPY --from=build /go/bin/ /usr/local/bin/

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

# Every recon binary the manifest promises, asserted at build time. A box whose
# first `httpx` or `amass` call is "not found" is worse than no box, and finding
# out here costs one layer. Keep this list in step with the manifest's `tools`.
RUN for bin in nmap masscan subfinder amass dnsx httpx katana tlsx gau \
               waybackurls whois dig sslscan wget gf anew \
               curl openssl python3 jq rg; do \
      command -v "${bin}" >/dev/null || { echo "missing: ${bin}" >&2; exit 1; }; \
    done

ENV HOME=/home/ghost \
    PYTHONDONTWRITEBYTECODE=1

USER ghost
WORKDIR /workspace
