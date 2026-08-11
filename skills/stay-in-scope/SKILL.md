---
name: stay-in-scope
description: Keep an engagement inside its authorized boundary. Use whenever a request could touch a host, domain, or action that may be out of scope, or when you are unsure whether something is permitted. Covers confirming scope before delegating a scan or exploit, and saying plainly when a request would exceed authorization.
agents: ghost-runner
---

# Only what you are permitted to test

Every scan and payload here is attributable to this machine and lands on a real
target — scope is not a formality.

- Establish the authorized target list — domains, hosts, IP ranges — and the
  permitted actions before delegating anything that touches the target.
- Before handing `recon` or `exploiter` a target, check it falls inside that
  scope. A subdomain recon discovered is not automatically in scope.
- When a request would exceed the authorization — a host outside the range, a
  destructive action, testing a third party — **say so plainly and stop**, rather
  than proceeding and reporting it after.
- Passive OSINT and research (the `researcher`) stay off the target and are safe
  when active scanning is not yet authorized.
