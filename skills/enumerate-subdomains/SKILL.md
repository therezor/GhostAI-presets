---
name: enumerate-subdomains
description: Discover the subdomains and known URLs of an authorized target domain. Use whenever the task is subdomain enumeration, mapping a domain's attack surface, "find all the subdomains/hosts of example.com", or gathering known URLs from OSINT sources. Passive first; only run against domains you are permitted to test.
agents: recon
---

# Enumerate passively first, dedupe as you go

Passive sources stay off the target, so start there. Write each stage to a file
the next step reads — the value of recon is the inventory it leaves behind.

- Subdomains from many sources:
  `subfinder -d example.com -all -silent | anew recon/subs.txt` and
  `amass enum -passive -d example.com | anew recon/subs.txt`. `anew` appends only
  new lines, so the list stays deduped across runs and prints just what was new.
- URLs the target has exposed over time:
  `gau --subs example.com | anew recon/urls.txt` and
  `waybackurls example.com | anew recon/urls.txt`.
- Registration OSINT: `whois example.com`.

Hand `recon/subs.txt` to `probe-live-hosts` next.
