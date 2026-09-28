# Timbuktu Cheat Sheet
> Incident response & digital forensics · QYVORA Framework · `git clone https://github.com/QYVORA/qyvora-timbuktu && cd _ && make build`

Offline forensic case analysis: evidence integrity, artifacts, filesystem/memory surfaces, logs, timelines, IOCs. Never modifies evidence.

---

## Quick start
```bash
timbuktu assess --sim                # risk 62/100 (high), deterministic
timbuktu case --sim                  # generate sample forensic case
timbuktu assess case.sim.json
```

## Commands
| Command | Purpose |
|---|---|
| `assess` | run pipeline against a case or `--sim` |
| `case` | generate a deterministic sample forensic case |
| `console` / `findings` / `evidence` / `report` / `rules` / `sources` / `target` / `capabilities` / `updates` / `version` | |

Global flags: `-o/--output` `-q/--quiet` `--no-color`

## Analysis rules (DFI-xxx)
| ID | Check | Sev |
|---|---|---|
| DFI-001 | Evidence integrity failure | critical |
| DFI-002 | Autorun persistence | high |
| DFI-003 | Suspicious scheduled task | high |
| DFI-004 | Service with temp image path | high |
| DFI-005 | Web shell deployed | critical |
| DFI-006 | Suspicious files in unusual locations | high |
| DFI-007 | Suspicious process activity | critical |
| DFI-008 | Credential material on disk | high |
| DFI-009 | Logon anomalies detected | medium |
| DFI-010 | Network indicators present | medium |
| DFI-011 | HOSTS file modification | medium |
| DFI-012 | Timeline coverage gap | low |
| DFI-013 | Masquerading system binary | high |

## Recipes
```bash
timbuktu assess --sim -o json
timbuktu case --sim && timbuktu assess case.sim.json -o json
```

## Gotchas / OPSEC
- `forensics.live` (live host acquisition) is **disabled** — offline case-file analysis only.
- Every evidence item is content-hashed and integrity-verified; the tool never modifies evidence — a good property to cite in chain-of-custody notes.

## See also
- `github.com/QYVORA/qyvora-timbuktu`
- `forensics/volatility.md`, `forensics/autopsy.md`
