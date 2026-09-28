# Nzinga Cheat Sheet
> OSINT / intelligence framework · QYVORA Framework (amber #FFB000) · `curl -fsSL https://raw.githubusercontent.com/QYVORA/qyvora-nzinga/main/install.sh | bash`

Answers "what can be learned about a target exclusively from public, open sources?" Not a scanner, not an exploit tool — collect, normalize, correlate, report. Every claim traces to a collected observation; absence is never reported as proof of absence.

---

## Quick start
```bash
nzinga assess --sim                          # offline demo pipeline
nzinga assess -y domain:example.com --sim    # demo dataset embed
nzinga sources list                          # enabled public sources
nzinga capabilities                          # advertised tool contract
nzinga report session --format json
```

## Pipeline (7 stages)
```
DISCOVER → COLLECT → NORMALIZE → CORRELATE → ANALYZE → VALIDATE → REPORT
```

## Entity / relationship model
- Entities: `Domain, Hostname, IP, Person, Email, Username, SocialAccount, Repository, Certificate, ASN, Organization`
- Edges: `resolves_to, hosts, belongs_to, registers, owns, uses, controls, attributed_to, related`

## Authorization
`-y / --authorized` (or `QYVORA_AUTHORIZED=true`) required before any **live** source runs; interactive prompt otherwise. `--sim` needs no authorization — runs against an offline dataset, zero network activity. `--dry-run` plans a run and lists sources that would execute, without touching the network.

## Rule set
`OSINT-001`…`OSINT-004` — findings dedupe by `Fingerprint()` (order-independent canonical hash).

## Recipes
```bash
nzinga assess -y domain:example.com --profile standard -o json
nzinga findings -f json
nzinga relationship graph
nzinga assess -y domain:example.com --dry-run   # see planned sources first
```

## Output formats
`terminal | json | markdown | html | yaml`, all from the same session/report model — no stub renderers.

## Exit codes
`0` success · `1` runtime failure · `2` usage error · `130` interrupted (128+SIGINT)

## Gotchas / OPSEC
- Read-only by design; all source ops start at Risk S1 (reversible, no remote state change).
- Failing sources degrade honestly — recorded in session errors, run continues.
- Human-focused OSINT (Person/Email/Username/SocialAccount) is treated as core, not an afterthought vs. infra recon.

## See also
- `github.com/QYVORA/qyvora-nzinga`
- `osint/theharvester.md`, `osint/sherlock.md`, `qyvora/anansi.md` (`osint.*` module overlap in TOHA3EE)
