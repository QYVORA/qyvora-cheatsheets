# Anansi Cheat Sheet
> Attack surface intelligence (web recon) · QYVORA Framework · `curl -fsSL https://raw.githubusercontent.com/QYVORA/qyvora-anansi/main/install.sh | bash`

Accent: green/emerald. Go CLI, Metasploit-style console (`anansiλ >`).

---

## Quick start
```bash
anansi scan target.com                     # one-shot full pipeline
anansi                                      # drop into console
printf 'set RHOSTS target.com\nrun\nexit\n' | anansi   # scripted session
```

## Pipeline phases
| Phase | What it does |
|---|---|
| 01 DISCOVERY | subdomain enumeration (crt.sh + wordlist + SAN) + DNS resolution |
| PROBE | HTTP/HTTPS surface probing |
| TLS | certificate analysis + SAN discovery |
| HEADERS | security header audit + CORS misconfig check |
| 05 PATHS | exposed endpoint/file detection (e.g. `.env`) |
| TECH | CMS/framework fingerprint + CVE version matching |
| 07 TAKEOVER | dangling CNAME / subdomain takeover detection |
| OSINT | passive intel gathering |
| CHAIN | exploit-chain assembly + ranking |

## Console commands
| Command | Description |
|---|---|
| `scan <target>` | full scan (also `anansi scan <target>` from shell) |
| `run [target]` | run using `RHOSTS` or given target, honors selected module |
| `set <opt> <value>` | set a scan option, e.g. `set THREADS 200` |
| `unset <opt>` | restore default |
| `options` / `show options` | show current values |
| `use <module>` | `discovery, probe, tls, headers, paths, tech, takeover, osint, chain` |
| `back` | deselect module |
| `search <text>` | search modules/options |
| `history` / `version` / `exit` | |

## Flags
| Flag | Default | Description |
|---|---|---|
| `-r, --recursive` | false | recursive subdomain brute-forcing |
| `-m, --mutate` | false | subdomain mutation brute-forcing |
| `-p, --ports` | 80,443 | comma-separated alt ports |
| `--delay` | 0 | ms between requests |
| `--deep` | false | larger wordlist + more path rules |
| `-o, --output` | terminal | `terminal\|json\|markdown\|html` |
| `--timeout` | 5 | per-request seconds |
| `-t, --threads` | 100 | concurrency |
| `--stealth` | false | random UA, jitter, skip crt.sh, reduce noise |
| `--modules` | all | comma list from the module names above |
| `-v, --verbose` | false | show all found/failed assets |

## Recipes
```bash
anansi scan target.com --deep --recursive -o json > report.json
anansi --stealth --threads 50 scan target.com
anansi --modules discovery,paths,takeover scan target.com
anansi --deep --threads 250            # starts console pre-loaded with these flags
```

## Output
Terminal summary includes subdomain count, findings by severity (`CRIT/HIGH/MED/LOW/INFO`), and a 0-100 risk score.

## Gotchas / OPSEC
- `--stealth` skips crt.sh and adds jitter/random UA — use on engagements that require lower noise.
- Companion to the full ANANSI microservice/API; CLI is the portable, laptop-side tool with identical detection logic.

## See also
- `github.com/QYVORA/qyvora-anansi`
- `qyvora/toha3ee.md` (`osint.*` modules overlap), `osint/theharvester.md`
