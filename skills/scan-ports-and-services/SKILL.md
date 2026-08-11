---
name: scan-ports-and-services
description: Scan an authorized host for open ports, services, and versions, and run cheap known-issue checks. Use whenever the task is a port scan, service/version detection, running nmap, or a fast wide sweep with masscan. Only run against hosts you are permitted to test; this traffic is attributable to this machine.
agents: recon
---

# Scan for ports, then fingerprint services

Only scan hosts you are permitted to — this traffic is attributable.

- Service and version detection on known ports:
  `nmap -sV -p 80,443,8080 example.com -oN recon/nmap.txt`. `-oN` writes a
  readable report.
- Cheap known-issue checks: add `--script vuln`.
- Faster SYN scan of every port: `nmap -sS -p- example.com` — NET_RAW is granted
  so this works, and nmap falls back to a connect scan on its own if it is not.
- Wide range, fast:
  `masscan -p1-65535 --rate 1000 192.0.2.0/24 -oL recon/masscan.txt`, then hand
  the open ports to `nmap -sV` for detail. Keep `--rate` sane so you do not drown
  the target.
