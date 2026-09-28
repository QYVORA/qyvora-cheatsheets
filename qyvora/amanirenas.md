# Amanirenas Cheat Sheet
> Offline mobile app (iOS/IPA) security assessment · QYVORA Framework · `git clone https://github.com/QYVORA/qyvora-amanirenas && cd _ && make build`

Static, offline-only analysis of app profiles/IPA snapshots — no runtime execution, no device required.

---

## Quick start
```bash
amanirenas assess --sim              # 14 findings, risk 56/100 (medium), deterministic
amanirenas profile --sim             # generate sample app profile
amanirenas assess profile.sim.json   # assess a real/generated profile
```

## Commands
| Command | Purpose |
|---|---|
| `assess` | run analysis pipeline against a profile or `--sim` |
| `profile` | generate a deterministic sample app profile |
| `console` | interactive assessment console |
| `findings` / `evidence` | inspect latest results |
| `report` | render latest report from disk |
| `rules` | list registered rules |
| `sources` | supported mobile sources + status |
| `target` | manage assessment targets |
| `capabilities` / `updates` / `version` | |

Global flags: `-o/--output` `-q/--quiet` `--no-color`

## Analysis rules (AMN-xxx)
| ID | Check | Sev |
|---|---|---|
| AMN-001 | Hardcoded secret in app | critical |
| AMN-002 | Insecure transport | critical |
| AMN-003 | Missing certificate pinning | medium |
| AMN-004 | Legacy WebView usage | medium |
| AMN-005 | Weak cryptography | medium |
| AMN-006 | Insecure local data storage | high |
| AMN-007 | Sensitive data copied to clipboard | medium |
| AMN-008 | Sensitive data in logs | low |
| AMN-009 | Excessive permissions | medium |
| AMN-010 | Outdated minimum OS version | low |
| AMN-011 | Ad-hoc signing without verified distribution | medium |
| AMN-012 | No jailbreak/tamper detection | medium |

## Recipes
```bash
amanirenas assess --sim -o json
amanirenas profile --sim && amanirenas assess profile.sim.json -o json
```

## Gotchas / OPSEC
- `mobile.runtime` (dynamic runtime assessment) and live device acquisition are **disabled by design** — offline profile/IPA analysis only, no app code executes.
- Secret values are redacted in output.

## See also
- `github.com/QYVORA/qyvora-amanirenas`
- `qyvora/jabari.md` (Android counterpart), `mobile/mobsf.md`
