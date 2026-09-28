# theHarvester & Sherlock Cheat Sheet
> Passive OSINT: emails/subdomains (theHarvester) + username enumeration (Sherlock) · OSINT

---

## theHarvester
```bash
theHarvester -d target.com -b all                  # all sources
theHarvester -d target.com -b google,bing,crtsh -l 500
theHarvester -d target.com -b shodan -f output.html
```

### Common sources (`-b`)
`google` `bing` `duckduckgo` `crtsh` `hackertarget` `shodan` `virustotal` `securitytrails` `otx` `threatminer` `all`

### Flags
| Flag | Meaning |
|---|---|
| `-d` | domain |
| `-b` | source(s) |
| `-l` | result limit |
| `-f` | output file (html/json/xml) |
| `-s` | start at result N (pagination) |
| `-v` | verify hosts via DNS resolution |

## Sherlock (username → account discovery)
```bash
sherlock username123
sherlock username123 --output results.txt
sherlock username123 --timeout 10 --print-found
```

### Flags
| Flag | Meaning |
|---|---|
| `--output <file>` | save results |
| `--timeout <secs>` | per-site timeout |
| `--print-found` | only show confirmed hits |
| `--csv` / `--xlsx` | structured output |
| `--proxy <url>` | route through a proxy |
| `--nsfw` | include adult sites in the check list |

## Recipes
```bash
# Combine domain OSINT with username pivot from an employee list
theHarvester -d target.com -b all -f target-osint.json
jq -r '.emails[]' target-osint.json | cut -d@ -f1 > possible-usernames.txt
while read u; do sherlock "$u" --print-found; done < possible-usernames.txt
```

## Gotchas / OPSEC
- Both tools are passive/OSINT-only by design — no active contact with the target beyond public API/search queries. Rate limits and API keys (Shodan, SecurityTrails) apply per source.
- theHarvester's Google/Bing scraping sources break often as those engines change their HTML — prefer API-key sources (`shodan`, `securitytrails`, `virustotal`) for reliability.

## See also
- https://github.com/laramies/theHarvester
- https://github.com/sherlock-project/sherlock
- `qyvora/nzinga.md`, `qyvora/anansi.md` (`osint` module), `qyvora/toha3ee.md` (`osint.*` modules)
