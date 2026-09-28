# Mansa Cheat Sheet
> Authorized wireless (WLAN) security assessment · QYVORA Framework · `curl -fsSL https://raw.githubusercontent.com/QYVORA/qyvora-mansa/main/install.sh | sh`

Console and CLI share identical commands; deterministic `--sim` mode needs no hardware.

---

## Quick start
```bash
mansa assess --sim             # full assessment, no hardware, deterministic
mansa                          # interactive console
```

## Console session
```
mansa
use wlan0
sim on
scan
analyze
findings
report --format markdown
exit
```

## Commands
| Command | Purpose |
|---|---|
| `assess` | full wireless assessment pipeline |
| `discover` | discover wireless interfaces/capabilities |
| `scan` | scan for networks and access points |
| `enumerate` | AP inventory with filtering |
| `observe` | observe clients/stations |
| `analyze` | analyze latest session for findings |
| `findings` / `evidence` | inspect results |
| `report` | render formatted report |
| `session` | inspect saved sessions |
| `events` | stored events for a session |
| `target` | manage assessment targets |
| `capabilities` | machine-readable contract |
| `version` / `updates` / `completion` | |

## Global flags
`-o/--output` `-y/--authorized` `-v/--verbose` `-q/--quiet` `--events` `--dry-run`

## Recipes
```bash
mansa capabilities -o json
mansa analyze -o json
mansa report -f json
mansa assess -y wlan0 --profile standard
```

## Gotchas / OPSEC
- Authorized use only — assess wireless networks you own or are authorized to evaluate.
- `--sim` is deterministic and CI-ready — good for demoing rule coverage before touching live hardware.

## See also
- `github.com/QYVORA/qyvora-mansa`
- `wireless/aircrack-ng.md`
