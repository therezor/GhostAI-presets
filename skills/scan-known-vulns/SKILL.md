---
name: scan-known-vulns
description: Sweep a web target for known CVEs, misconfigurations, exposures, and dangerous server defaults. Use whenever the task is a vulnerability scan, running nuclei, a nikto scan, or checking a site for known issues. For authorized testing only. Confirm anything it flags with confirm-injection before reporting it.
agents: exploiter
---

# Sweep with nuclei, then nikto

- Run the baked template library:
  `nuclei -u https://example.com -t /opt/ghost/nuclei-templates -duc -je findings/nuclei.json`.
  `-t` points at the baked templates and `-duc` skips the update check — there is
  no reason to fetch templates at runtime. Narrow noisy runs with
  `-severity high,critical`.
- Web-server issues — dangerous files, outdated software, misconfiguration:
  `nikto -h https://example.com -o findings/nikto.txt -Format txt`.
- A scanner hit is a lead, not a finding. Triage the JSON and **confirm each
  candidate by demonstrating it** (see confirm-injection) before it goes in the
  report.
