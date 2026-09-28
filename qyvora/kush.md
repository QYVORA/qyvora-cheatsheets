# Kush Cheat Sheet
> Offline malware sample analysis · QYVORA Framework · `git clone https://github.com/QYVORA/qyvora-kush && cd _ && make build`

Static-only analysis: hashes, metadata, strings, network indicators, IOC extraction, threat classification. Never executes the sample.

---

## Quick start
```bash
kush assess --sim                    # risk 73/100 (high), deterministic
kush sample --sim                    # generate sample malware document
kush assess sample.sim.json
```

## Commands
| Command | Purpose |
|---|---|
| `assess` | run pipeline against a sample or `--sim` |
| `sample` | generate a deterministic sample malware document |
| `console` / `findings` / `evidence` / `report` / `rules` / `sources` / `target` / `capabilities` / `updates` / `version` | |

Global flags: `-o/--output` `-q/--quiet` `--no-color`

## Analysis rules (KSH-xxx)
| ID | Check | Sev |
|---|---|---|
| KSH-001 | Suspicious process-spawning imports | high |
| KSH-002 | Packed or high-entropy binary | medium |
| KSH-003 | Unsigned binary with no publisher | medium |
| KSH-004 | Persistent autostart mechanism | high |
| KSH-005 | Command-and-control indicators | critical |
| KSH-006 | Encoded command launcher | high |
| KSH-007 | Embedded staged payload | medium |
| KSH-008 | Browser user-agent impersonation | medium |
| KSH-009 | Socket imports with process access | medium |
| KSH-010 | Verified high-confidence IOC catalog | informational |
| KSH-011 | Behavioral anomalies from sandbox | high |
| KSH-012 | Dynamic execution on developer host refused | low |
| KSH-013 | Process injection primitives | high |
| KSH-014 | Writable-and-executable section | medium |

## Recipes
```bash
kush assess --sim -o json
kush sample --sim && kush assess sample.sim.json -o json
```

## Gotchas / OPSEC
- Samples are **never executed** on the developer host; dynamic execution is refused honestly (`KSH-012`) and behavioral findings (`KSH-011`) only come from an isolated sandbox you attach explicitly.
- `KSH-010` only reports IOCs corroborated across multiple surfaces of the sample — treat it as the high-confidence subset, not the full indicator list.

## See also
- `github.com/QYVORA/qyvora-kush`
- `reversing/gdb-pwndbg.md`, `qyvora/aksum.md` (static binary analysis overlap)
