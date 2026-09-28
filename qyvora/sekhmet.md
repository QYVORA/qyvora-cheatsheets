# Sekhmet Cheat Sheet
> Baseline-aware, feedback-driven fuzzing · QYVORA Framework · `curl -fsSL https://raw.githubusercontent.com/QYVORA/qyvora-Sekhmet/main/install.sh | bash`

Profiles a target's *normal* behavior first, then fuzzes and classifies crashes/hangs/anomalies relative to that baseline.

---

## Quick start
```bash
sekhmet target set --name example --kind process --cmd "./target" --arg "{fuzz}"
sekhmet baseline --target ./target --samples 64
sekhmet fuzz --target ./target --runs 100000 --jobs 4
sekhmet crashes --session <id>
sekhmet report  --session <id> --format json
```
No binary handy? `sekhmet target set --name sim --kind simulation && sekhmet baseline --target sim && sekhmet fuzz --target sim --runs 100000`

## Execution modes
`process` (`{fuzz}`/`{stdin}` templates) · `HTTP` (fuzzed payloads to an endpoint) · `simulation` (deterministic built-in target)

## Pipeline
```
Target → Baseline → Corpus → Mutate → Execute → Classify → Dedup → Report
```

## Commands
| Command | Purpose |
|---|---|
| `baseline` | profile normal behavior |
| `fuzz` | run a campaign |
| `analyze` | classify/summarize session findings |
| `corpus` | manage seed corpus (import/crop/list) |
| `crashes` | deduplicated crash list |
| `minimize` | delta-debug an interesting input to minimal repro |
| `replay` | reproduce an input against a target |
| `session` | list/show fuzzing sessions |
| `report` | render campaign report |
| `target` | manage targets |
| `wordlists` | SecLists search/load without vendoring the whole set |
| `capabilities` / `version` | |

## Mutation & scheduling
17 structured operators (bit/byte, block, dictionary insert, JSON structure, boundary, length, splice…) driven by seeded RNG; power scheduling modes: `fast, explore, exploit, rare, balanced, adaptive`, driven by novelty scoring over behavioral/edge/block coverage.

## Config precedence
`CLI flags > QYVORA_SEKHMET_* env vars > config file > defaults`. `QYVORA_SEKHMET_SESSION_DIR` relocates session storage.

## Recipes
```bash
sekhmet fuzz --target ./target --runs 500000 --jobs 8 --events stdout
sekhmet minimize --session <id> --input crash-0001
sekhmet wordlists search sqli
```

## Gotchas / OPSEC
- Local targets scoped to the declared path; remote (HTTP/network) targets require explicit authorization acknowledgment.
- `--dry-run` audits a campaign without executing anything.
- Crash dedup uses SHA-256 signature — a large "crash count" often collapses to a handful of unique bugs after `sekhmet crashes`.

## See also
- `github.com/QYVORA/qyvora-Sekhmet`
- `web/ffuf.md` (HTTP fuzzing overlap), `reversing/gdb-pwndbg.md` (crash triage)
