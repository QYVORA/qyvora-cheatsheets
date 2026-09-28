# TOHA3EE Cheat Sheet
> Local & network security assessment (recon → MITM → post-ex) · QYVORA Framework (red) · `curl -fsSL https://raw.githubusercontent.com/qyvora/qyvora-toha3ee/main/scripts/install.sh | sh`

"The red hunter" — 73 modules across 10 categories, `.toha3ee` scripting language, REPL/wizard/one-shot.

---

## Quick start
```bash
sudo ./toha3ee --iface eth0                       # interactive console
sudo ./toha3ee wizard --iface eth0                # guided wizard
sudo ./toha3ee --eval "net.scan; net.show" --iface eth0
sudo ./toha3ee run --iface eth0 caplets/basic.cap  # caplet script
./toha3ee --no-sudo build scripts/full-pipeline.toha3ee   # dry-run a script
sudo ./toha3ee script --iface eth0 scripts/full-pipeline.toha3ee
```
Requires root/`CAP_NET_ADMIN`+`CAP_NET_RAW` for most modules; runs under sudo by default (`--no-sudo` to skip).

## Module categories (73 total — run `toha3ee modules` for the live catalogue)
| Category | Examples |
|---|---|
| recon | `net.scan` `net.ping` `net.traceroute` `net.osdetect` `service.synscan` `service.fingerprint` `service.tls` `web.dir` `cve.suggest` |
| enum | `smtp.enum` `snmp.enum` `ldap.enum` `nfs.enum` `smb.enum` `net.ip6sweep` |
| osint | `osint.dns` `osint.whois` `osint.ct` `osint.asn` `osint.shodan` `osint.bucket` `osint.wayback` `osint.github` `osint.hibp` `osint.dork` |
| mitm | `arp.spoof` `dns.spoof` `dns.rebind` `dhcp.rogue` `dhcp.starve` `icmp.redirect` `ipv6.ra` `ipv6.ndp` `llmnr.poison` `wpad.poison` |
| espionage | `http.harvest` `http.proxy` `https.proxy` `ssl.strip` `phish.inject` |
| auth | `default.creds` `ntlm.relay` `smb.signing` `smb.kerberoast` `auth.spray` `auth.brute` `auth.userenum` `auth.asrep` |
| web | `web.misconfig` |
| switch | `switch.flood` `switch.portsteal` `switch.vlanhop` `switch.cdp` `switch.stp` |
| wireless | `wlan.scan` `wlan.deauth` `wlan.handshake` `wlan.eviltwin` `wlan.pmkid` `wlan.beaconflood` `wlan.karma` |
| post | `report.generate` `session.replay` `pcap.export` |

## Console
```
toha3eeλ> modules recon        # filter catalogue by category
toha3eeλ> on net.scan          # run a module
toha3eeλ> net.show             # discovered hosts
toha3eeλ> net.profile          # profile + ranked attack vectors
toha3eeλ> set arp.spoof.targets 10.0.0.5   # dotted module.key config
toha3eeλ> config                # dump all set values
toha3eeλ> help / quit
```
Status glyphs: `[*]` info, `[+]` success, `[!]` warning, `[>]` system, `[-]` neutral, `[x]` error, `[OK]` verified.

## `.toha3ee` scripting
```toha3ee
set net.scan.targets -> "192.168.8.0/24"
on net.scan
wait for net.scan
_hosts -> [$(net.hosts)]
echo -> "found $(_hosts.size) hosts"
if $(hosts.count) > 1
    on arp.spoof targets "192.168.8.0/24"
    sleep -> 30
    off arp.spoof
end
for each _h in $(_hosts)
    repeat 3 times
        exec -> net.show
        break
    end
end
report -> "assessment.md"
```
Statements: `set/get`, `on|start|run`, `off|stop`, `wait for <module> [max <secs>]`, `sleep`, `echo|say|print`, `show <module>`, `report <file>`, `exec`, `if/else/end`, `for each _x in <list>`, `repeat N times`, `while`, `break`, `continue`, `stop`. Interpolation via `$(...)`: `hosts.count`, `net.hosts`, `creds.count`, `sessions.count`, `running.list`, `iface.ip/cidr/mac/gateway`, `config.<module.key>`.

## Recipes
```bash
sudo ./toha3ee --eval "net.recon; net.profile" --iface eth0   # deep recon: synscan->fingerprint->enum
sudo ./toha3ee --eval "arp.spoof; https.proxy; report.generate" --iface eth0
sudo ./toha3ee run --iface eth0 caplets/mitm-arp.caplet
```

## Stealth (always on)
Randomized/jittered by default on every packet-sending module: shuffled probe order (`stealth_shuffle`), bursty pacing (`stealth_jitter/burst/pause`), randomized ARP padding (`stealth_pad`), varied SYN source port/TTL/IPID/seq/window (`stealth_ports/ttl/id`), rotated HTTP user agents. Tune per-module: `set net.scan.stealth_jitter 5ms`.

## Gotchas / OPSEC
- **Only on networks you own or are explicitly authorized to test** — actively redirects/poisons/decrypts traffic; illegal otherwise in most jurisdictions.
- Every attack module implements `Meta/Preflight/Run/Verify/Cleanup`; a safety lifecycle guarantees cleanup/rollback even on panic or SIGINT.
- Stacked queries / MITM stay isolated to `internal/attacks` categories — always check `Preflight` output before confirming a risky module.

## See also
- `github.com/QYVORA/qyvora-toha3ee` · man pages: `toha3ee.1`, `scripting.7`, `security.7`
- `network/bettercap.md`, `network/wireshark.md`, `ad-windows/*` for deeper AD-specific auth attacks
