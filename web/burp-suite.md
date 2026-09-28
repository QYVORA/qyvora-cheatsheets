# Burp Suite Cheat Sheet
> Web app proxy / interception / scanning · Web · GUI tool (Community/Pro)

---

## Quick start
1. Launch Burp, use the embedded Chromium or set browser proxy to `127.0.0.1:8080`.
2. Install the CA cert (`http://burp` → CA Certificate) into the browser/OS trust store for HTTPS interception.
3. Browse the target with Proxy → Intercept on to capture traffic.

## Most-used panels
| Panel | Use |
|---|---|
| Proxy → HTTP history | full request/response log |
| Repeater | manually replay/tweak a single request |
| Intruder | automated fuzzing across payload positions |
| Sequencer | randomness/entropy analysis of tokens |
| Decoder | encode/decode (URL, Base64, hex, hash) |
| Comparer | diff two requests/responses |
| Target → Site map | crawled structure, scope tree |
| Extender / BApp Store | plugins (Logger++, Autorize, JWT Editor, etc.) |

## Intruder attack types
| Type | Behavior |
|---|---|
| Sniper | one payload set, cycles through each position |
| Battering ram | same payload in all positions simultaneously |
| Pitchfork | parallel payload sets, one per position |
| Cluster bomb | all combinations across payload sets |

## Common workflows
```
1. Right-click request in Proxy history -> Send to Repeater -> tweak/replay
2. Right-click -> Send to Intruder -> mark § positions -> choose attack type -> load payload list -> Start attack
3. Match/Replace rule (Proxy -> Options) to auto-strip headers like CSP for testing
4. Set scope (Target -> Scope) then filter Proxy history to "Show only in-scope items"
```

## Useful extensions
`Logger++` (better history/filtering), `Autorize` (authz testing), `JWT Editor` / `JSON Web Tokens`, `Turbo Intruder` (high-throughput race conditions), `Param Miner` (hidden params/headers), `Active Scan++`.

## Recipes
```
# Test for IDOR: swap ID params via Intruder pitchfork across two accounts' sessions
# Race condition: use Turbo Intruder's race-single-packet template
# JWT tampering: JWT Editor -> alg:none / key confusion / weak HMAC secret bruteforce
```

## Output & reporting
Pro: Target → Issues → generate HTML/XML report. Community lacks the automated scanner — rely on manual findings + Logger++ export.

## Gotchas / OPSEC
- Community edition throttles Intruder — for heavy fuzzing use `ffuf`/`wfuzz` instead.
- Remember to disable interception (`Ctrl+Shift+I`... actually Proxy toggle) when not actively editing, or normal browsing stalls.
- Upstream proxy chaining (Burp → corporate proxy) is configured under User options → Connections.

## See also
- https://portswigger.net/burp/documentation
- `web/ffuf.md`, `web/sqli-cheatsheet.md`
