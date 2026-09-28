# Sundiata Cheat Sheet
> Identity & access security assessment · QYVORA Framework · `git clone https://github.com/QYVORA/qyvora-sundiata && cd _ && make build`

Offline identity-directory analysis: account enumeration, auth/MFA posture, credential exposure, privilege mapping, attack paths.

---

## Quick start
```bash
sundiata assess --sim                # risk 80/100 (critical), deterministic
sundiata directory --sim             # generate sample identity directory
sundiata assess directory.sim.json
```

## Commands
| Command | Purpose |
|---|---|
| `assess` | run pipeline against a directory or `--sim` |
| `directory` | generate a deterministic sample identity directory |
| `console` / `findings` / `evidence` / `report` / `rules` / `sources` / `target` / `capabilities` / `updates` / `version` | |

Global flags: `-o/--output` `-q/--quiet` `--no-color`

## Analysis rules
| ID | Check | Sev |
|---|---|---|
| SDT-001 | Plaintext credential exposure | critical |
| SDT-002 | Credential rotation overdue | medium |
| SDT-003 | Privileged identity without MFA | high |
| SDT-004 | Password never expires | medium |
| SDT-005 | Disabled identity retains access | medium |
| SDT-006 | Credential reuse across identities | high |
| SDT-007 | Secret material in files | high |
| SDT-008 | Excess privilege membership | high |
| SDT-009 | Identity attack path to sensitive group | critical |
| SDT-010 | Impersonation relationship | medium |
| SDT-011 | Legacy privileged account | high |
| SDT-012 | Excessive session lifetime | low |
| SDT-013 | Legacy authentication hash exposure | high |

## Recipes
```bash
sundiata assess --sim -o json
sundiata directory --sim && sundiata assess directory.sim.json -o json
```

## Gotchas / OPSEC
- `identity.live` (live source collection) is **disabled** — offline snapshots only.
- Credential values are redacted before output and never stored.

## See also
- `github.com/QYVORA/qyvora-sundiata`
- `qyvora/shaka.md` (live AD counterpart), `passwords/hashcat.md`, `ad-windows/bloodhound.md`
