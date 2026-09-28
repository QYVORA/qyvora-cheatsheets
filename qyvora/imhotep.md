# Imhotep Cheat Sheet
> Offline cloud snapshot security assessment · QYVORA Framework · `git clone https://github.com/QYVORA/qyvora-imhotep && cd _ && make build`

IAM, storage, network, container and secret exposure — from recorded snapshots, no live provider API access.

---

## Quick start
```bash
imhotep assess --sim                 # risk 76/100 (high), deterministic
imhotep snapshot --sim               # generate a sample cloud snapshot
imhotep assess snapshot.sim.json
```

## Commands
| Command | Purpose |
|---|---|
| `assess` | run pipeline against snapshot or `--sim` |
| `snapshot` | generate a deterministic sample snapshot |
| `console` | interactive console |
| `providers` | supported cloud providers + status |
| `findings` / `evidence` / `report` / `rules` / `target` / `capabilities` / `updates` / `version` | |

Global flags: `-o/--output` `-q/--quiet` `--no-color`

## Analysis rules
| ID | Check | Sev |
|---|---|---|
| IAM-001 | Wildcard action granted | high |
| IAM-002 | Wildcard resource scope | high |
| STG-001 | Publicly readable storage | high |
| STG-002 | Publicly writable storage | critical |
| STG-003 | Unencrypted storage at rest | medium |
| NET-001 | Admin port exposed to internet | high |
| NET-002 | Publicly accessible database | critical |
| NET-003 | Publicly exposed compute workload | high |
| DBE-001 | Unencrypted database at rest | medium |
| CNT-001 | Privileged container capability | high |
| CNT-002 | Host network namespace | medium |
| CNT-003 | Immutability-breaking image tag | low |
| CNT-004 | Container runs as root | medium |
| SEC-001 | Hardcoded secret material | critical |

## Recipes
```bash
imhotep assess --sim -o json
imhotep snapshot --sim && imhotep assess snapshot.sim.json -o json
```

## Gotchas / OPSEC
- `cloud.live` (live provider API collection) is **disabled** — snapshot-driven only, provider SDKs not wired up.
- Secret values are redacted at collection time, never stored/printed.

## See also
- `github.com/QYVORA/qyvora-imhotep`
- `cloud/scoutsuite.md`, `cloud/prowler.md`
