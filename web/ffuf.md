# ffuf Cheat Sheet
> Fast web fuzzer (dirs, params, vhosts, subdomains) · Web · `go install github.com/ffuf/ffuf/v2@latest`

---

## Quick start
```bash
ffuf -w wordlist.txt -u https://target.com/FUZZ
ffuf -w wordlist.txt -u https://target.com/FUZZ -mc 200,301,302,403
```

## Most-used commands
| Task | Command |
|---|---|
| Directory brute-force | `ffuf -w dirs.txt -u https://target.com/FUZZ` |
| Extension brute-force | `ffuf -w words.txt -u https://target.com/FUZZ -e .php,.bak,.zip` |
| Vhost discovery | `ffuf -w subs.txt -u https://target.com -H "Host: FUZZ.target.com" -fs <baseline_size>` |
| Subdomain enum | `ffuf -w subs.txt -u https://FUZZ.target.com -mc 200` |
| POST param fuzz | `ffuf -w vals.txt -u https://target.com/login -X POST -d "user=admin&pass=FUZZ" -mc 200` |
| Multiple FUZZ points | `ffuf -w users.txt:FUZ1 -w pass.txt:FUZ2 -u https://target.com/login -X POST -d "user=FUZ1&pass=FUZ2"` |
| Recursive dir fuzz | `ffuf -w dirs.txt -u https://target.com/FUZZ -recursion -recursion-depth 2` |

## Common flags
| Flag | Meaning |
|---|---|
| `-mc` | match status codes |
| `-fc` | filter (exclude) status codes |
| `-fs` | filter by response size |
| `-fw` | filter by word count |
| `-fl` | filter by line count |
| `-t` | threads (default 40) |
| `-p` | delay between requests, e.g. `0.1-0.5` |
| `-H` | custom header (repeatable) |
| `-b` | cookie |
| `-c` | colorized output |
| `-o / -of` | output file / format (json,csv,html,md) |
| `-recursion` | recurse into found directories |

## Recipes
```bash
# Establish a baseline to auto-filter soft-404 pages
ffuf -w dirs.txt -u https://target.com/FUZZ -fs $(curl -s -o /dev/null -w "%{size_download}" https://target.com/nonexistent12345)

# Chain: dirs -> for each hit, fuzz extensions
ffuf -w dirs.txt -u https://target.com/FUZZ -mc 200,301,403 -o hits.json -of json
jq -r '.results[].url' hits.json | while read u; do ffuf -w ext.txt -u "$u.FUZZ" -mc 200; done

# API fuzzing with JSON body
ffuf -w ids.txt -u https://target.com/api/user -X POST -H "Content-Type: application/json" -d '{"id":"FUZZ"}' -mc 200
```

## Output & reporting
`-o out.json -of json` then pipe through `jq`, or `-of html` for a shareable static report.

## Gotchas / OPSEC
- Always set `-fs`/`-fc` after a baseline check — most sites return `200` for "not found" pages, which floods results without filtering.
- High `-t` values can trip WAF rate limits; pair with `-p` jitter on sensitive engagements.

## See also
- https://github.com/ffuf/ffuf
- `web/burp-suite.md`, `web/gobuster.md`, `qyvora/anansi.md` (paths module overlap)
