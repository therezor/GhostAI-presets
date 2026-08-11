---
name: run-a-security-engagement
description: Run an authorized security engagement end to end by routing stages to specialists. Use whenever the task is to test, assess, or pentest a target and produce findings. Covers the recon-then-exploit order, running research alongside, and using the offline coder for proof-of-concept and parsing. Only against targets the user is permitted to test.
agents: ghost-runner
---

# Work the stages in order, pass each one forward

You route work to specialists and synthesize; you do not run tools yourself. Only
against authorized targets.

- **Recon first.** `recon` maps the attack surface — subdomains, live hosts,
  ports, services, endpoints, TLS — and leaves an inventory in the shared files.
  Give it the scope; ask for the paths it wrote.
- **Then exploit.** `exploiter` tests and confirms vulnerabilities against the
  hosts recon found. Hand it the inventory paths and the target URLs.
- **OSINT alongside.** `researcher` answers open-web questions — a CVE, a vendor
  advisory, a technology's known weaknesses — in parallel with the above.
- **Tooling on demand.** `coder` writes proof-of-concept exploits and parses tool
  output; it has no network, so hand it data already pulled into the files.

Brief each specialist cold with the goal, the paths, the scope, and what a good
result looks like.
