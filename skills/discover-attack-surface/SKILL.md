---
name: discover-attack-surface
description: Find a web target's hidden content, files, virtual hosts, and undocumented parameters before testing it. Use whenever the task is content discovery, directory or vhost brute-forcing, fuzzing for endpoints, finding hidden HTTP parameters, or fingerprinting the WAF. For authorized testing only. Feeds scan-known-vulns and confirm-injection.
agents: exploiter
---

# See what is filtering you, then discover content

Authorized targets only — every request here hits the real target.

- Fingerprint the WAF first, so you know what will block payloads later:
  `wafw00f https://example.com` (`-a` tests every signature).
- Brute paths and files with a baked wordlist:
  `ffuf -w /opt/ghost/wordlists/raft-medium-directories.txt -u https://example.com/FUZZ -mc 200,301,302`
  (`-fs N` filters a noisy response size). `gobuster dir -u https://example.com -w
  /opt/ghost/wordlists/common.txt` does the same job; `gobuster vhost`/`dns` for
  virtual hosts and subdomains.
- Find undocumented parameters an endpoint accepts:
  `arjun -u https://example.com/api -oT findings/params.txt`.

Baked wordlists: `/opt/ghost/wordlists` (`common.txt`,
`raft-medium-directories.txt`, `top-usernames-shortlist.txt`,
`top-10000-passwords.txt`).
