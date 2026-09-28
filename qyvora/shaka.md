# Shaka Cheat Sheet
> Authorized Active Directory / Windows security assessment · QYVORA Framework (royal blue & gold) · `curl -fsSL .../qyvora-shaka/main/install.sh | bash`

Single-domain assessment tool — scoped to one directory plus its declared trusts, never a subnet sweep. Read-only in this release (no state changes).

---

## Quick start
```bash
shaka                          # console (Metasploit-style REPL)
shaka assess --sim             # offline deterministic demo (corp.example.com)
shaka assess -y ldap://dc.corp.example.com --profile standard
shaka report                   # re-render report from saved session anytime
```

## Pipeline
```
DISCOVER → VERIFY → DEEPEN → CORRELATE → ANALYZE → REPORT
```
| Stage | What happens |
|---|---|
| DISCOVER | domains, domain controllers, base DN; seeds the graph |
| VERIFY | follow-up queries confirm discovery/enumeration results |
| DEEPEN | nested memberships and deeper object detail expanded |
| CORRELATE | relationship graph completed (nodes, edges, trusts) |
| ANALYZE | rule engine + identity, trust, Kerberos, attack-path analysis |
| REPORT | findings, evidence, risk rendered |

## Profiles
`quick` `standard` `deep` `directory` `authentication` `trust` `identity` `compliance` `research`

## Rule set
`ADM-001`…`ADM-006`, `AUTH-001` (deterministic, hashed/deduplicated evidence)

## Output formats
`terminal | json | markdown | html | yaml` — all render from the same session model.

## Flags
| Flag | Meaning |
|---|---|
| `-y / --authorized` | asserts operator authorization (or `QYVORA_AUTHORIZED=true`) |
| `--sim` | offline demo simulator, auto-authorized, no network |
| `--events <target>` | JSONL run/stage/finding event stream |
| `shaka capabilities` / `shaka tools` | AI-ready tool catalog with risk/auth metadata |

## Recipes
```bash
shaka assess --sim --profile deep -o json
shaka assess -y ldap://dc.corp.example.com --profile trust
shaka capabilities -o json
```

## Gotchas / OPSEC
- **Not a mass scanner** — one domain (plus its declared trusts) per assessment.
- Every run passes an authorization gate; no silent path proceeds without it.
- Foundation release is read-only discovery/enumeration/analysis — no exploitation.

## See also
- `github.com/QYVORA/qyvora-shaka`
- `ad-windows/bloodhound.md`, `ad-windows/netexec.md`, `ad-windows/impacket.md`
