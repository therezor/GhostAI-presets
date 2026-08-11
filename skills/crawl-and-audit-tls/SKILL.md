---
name: crawl-and-audit-tls
description: Crawl a live site for endpoints, forms, and parameters, and audit its TLS. Use whenever the task is to map a site's URLs and parameters for later testing, crawl for endpoints, pull injectable-looking URLs, or check TLS versions/ciphers/certificate names. Runs against authorized live hosts found in earlier recon.
agents: recon
---

# Crawl for endpoints, pull the interesting ones, check TLS

- Crawl a live site:
  `katana -u https://example.com -d 2 -jc -o recon/endpoints.txt`. `-d` is depth;
  `-jc` follows JavaScript-derived endpoints from static analysis. There is no JS
  engine, so client-rendered routes still will not appear.
- Pull candidates for a bug class out of the crawl:
  `cat recon/endpoints.txt | gf xss | anew recon/xss-candidates.txt` (also `ssrf`,
  `sqli`, `redirect`).
- TLS posture: `sslscan example.com` for versions and ciphers;
  `tlsx -u example.com:443 -san -cn -silent` for the names a certificate covers
  (`-l hosts.txt` across many hosts).
