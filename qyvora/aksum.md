# Aksum Cheat Sheet
> Binary security assessment / reverse engineering · QYVORA Framework (gold) · `curl -fsSL https://raw.githubusercontent.com/QYVORA/qyvora-aksum/main/install.sh | bash`

Terminal-first ELF binary analysis (x86/x86-64). Never guesses — unknown properties are reported `unknown`, findings carry explicit confidence.

---

## Quick start
```bash
aksum binary ./firmware.elf              # format/arch/PIE/NX/RELRO/canary
aksum functions ./app -f json > funcs.json
aksum xrefs ./app --string "Usage: %s"
aksum analyze ./target --report report.json --min-severity low
```

## Pipeline
| Stage | Command | Output |
|---|---|---|
| 01 | `aksum binary` | format, arch, linking, PIE/NX/RELRO/canary/fortify (tri-state) |
| 02 | `aksum sections/segments/symbols/imports` | structural enumeration + risky API classification |
| 03 | `aksum strings` | string extraction w/ URL/path/cmd/crypto/credential classes (ELF+raw) |
| 04 | `aksum disassemble` | linear-sweep x86/x86-64 disassembly |
| 05 | `aksum functions` | multi-source function discovery w/ provenance+confidence |
| 06 | `aksum calls / cfg` | call graph + per-function CFG metrics |
| 07 | `aksum xrefs` | cross-refs to code/data (`--addr`, `--string`) |
| 08 | `aksum analyze` | full pipeline, dataflow-resolved calls, all rules, dedup findings |
| 09 | `aksum surface` | attack-surface aggregation |

## Console
```
$ aksum
aksum > open /usr/bin/ls
aksum [/usr/bin/ls] > functions --min-confidence high
aksum [/usr/bin/ls] > xrefs --string "Usage"
aksum [/usr/bin/ls] > analyze --min-severity low
aksum [/usr/bin/ls] > quit
```
Tab completion, aliases (`?`,`b`,`syms`,`dis`…), `~/.aksum_history`, `help <command>`, `--json` appendable to any command. Scriptable via stdin pipe.

## Findings model
Confidence ladder: `OBSERVED → CANDIDATE → SUSPECTED → VALIDATED → CONFIRMED` (CONFIRMED reserved, no executor bundled). Severity `info→critical`. Evidence kinds: `property, import, string, segment, callsite`. Deterministic finding IDs: `AKS-<CATEGORY>-<hash>`.

Builtin rules cover: missing NX/PIE/RELRO/canary, writable+executable segments, dangerous imports (`gets, strcpy, sprintf, system, popen`…), weak-crypto/credential-shaped strings, process-execution surface.

## Exit codes
| Code | Meaning |
|---|---|
| 0 | success |
| 1 | runtime failure |
| 2 | usage error |
| 3 | unsupported target (no decoder for arch yet) |
| 130 | interrupted |

## Recipes
```bash
aksum analyze ./target --min-severity low --report report.json
aksum functions ./app --min-confidence high -f json
echo 'help' | aksum          # scripted, no banner, side-effect free
aksum updates                # verified self-update
```

## Output
JSON report is schema-versioned (`schema_version`), plus a JSONL event stream (`--events stdout|stderr|file`): `scan.started`, `phase.started/completed`, `validation.started/completed`, `finding.discovered`, `report.generated`, `scan.completed`.

## Gotchas / OPSEC
- Only scan software you own or have explicit permission to assess.
- ELF only today (32/64-bit, either endianness); disassembly is x86/x86-64 only — other arches enumerate but refuse to disassemble (`exit 3`). PE/Mach-O planned.
- A dangerous import alone is a `CANDIDATE`, never a verdict — treat findings as leads, not conclusions.

## See also
- `github.com/QYVORA/qyvora-aksum`
- `reversing/gdb-pwndbg.md`, `reversing/radare2.md`, `reversing/objdump.md`
