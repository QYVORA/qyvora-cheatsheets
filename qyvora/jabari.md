# Jabari Cheat Sheet
> Authorized Android security assessment · QYVORA Framework (lime) · alias `androidsec` · `curl -fsSL https://raw.githubusercontent.com/QYVORA/qyvora-jabari/main/install.sh | bash`

Pipeline: `Discovery → Enumeration → Analysis → Validation → Evidence → Risk → Reporting`. Two target modes: USB (ADB) or a specific authorized IP — never subnet scanning.

---

## Quick start
```bash
jabari                                   # console (Metasploit-style REPL)
jabari assess usb                        # assess a connected device (interactive auth)
jabari assess ip 192.168.1.50            # assess a specific authorized IP
jabari assess ip 192.168.1.50 -y --profile deep --json   # non-interactive automation
```

## Console
```
jabariλ > target usb
jabariλ > assess
jabariλ > help
jabariλ > quit
```

## Profiles
`quick` `standard` `deep` `application` `device` `network` `compliance` `research`

## Rule set
`AND-001` … `AND-007` (initial builtin Android checks)

## Flags / global
| Flag | Meaning |
|---|---|
| `-y` | non-interactive authorization confirmation |
| `--profile <name>` | assessment profile |
| `--json` | JSON report |
| `--dry-run` | plan without executing |
| `--events <stdout\|stderr\|file>` | JSONL run/stage/finding event stream |

## Recipes
```bash
jabari assess usb --profile quick
jabari assess ip 10.0.0.42 -y --profile compliance -o json > jabari-report.json
jabari updates                     # verified self-update, checksum-checked
```

## Output
```
Findings
  Critical       0
  High           1
  Medium         2
  Low            4
  Informational  7
Key findings
  [HIGH] Debuggable Production Device (confirmed)
```

## Gotchas / OPSEC
- Explicit target authorization required every run; no silent path around the gate.
- Android-centric only — given a network target it assesses that address alone, never the surrounding subnet.
- Requires Android platform-tools (`adb`) on the host for USB targets.

## See also
- `github.com/QYVORA/qyvora-jabari`
- `mobile/frida.md`, `mobile/mobsf.md` for deeper dynamic/static Android analysis
