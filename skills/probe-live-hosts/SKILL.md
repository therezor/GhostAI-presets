---
name: probe-live-hosts
description: Turn a list of hosts into the ones that resolve and the ones actually serving HTTP, with their status, title, server, and tech. Use after subdomain enumeration, or whenever the task is to find live hosts, resolve a host list, or fingerprint what is running on discovered hosts. Feeds the port-scan and crawl stages.
agents: recon
---

# Resolve, then probe for live HTTP

- Keep only the names that resolve:
  `dnsx -l recon/subs.txt -a -resp -silent | anew recon/resolved.txt`. Add
  `-cname`/`-mx`/`-txt` when you want those records too.
- Find the ones serving HTTP, one line each:
  `httpx -l recon/resolved.txt -sc -title -server -tech-detect -silent -o recon/live.txt`.
  `-sc` is the status code, `-tech-detect` fingerprints the stack.

The `recon/live.txt` lines are the input for `scan-ports-and-services` and
`crawl-and-audit-tls`.
