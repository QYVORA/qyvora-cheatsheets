# Wireshark / tshark Cheat Sheet
> Packet capture & analysis · Network · `apt install wireshark tshark`

---

## Quick start
```bash
sudo tshark -i eth0                          # live capture, CLI
sudo tshark -i eth0 -w capture.pcap          # write to file
wireshark capture.pcap                       # open in GUI
```

## Display filters (GUI filter bar / `tshark -Y`)
| Filter | Matches |
|---|---|
| `ip.addr == 10.0.0.5` | traffic to/from an IP |
| `tcp.port == 443` | TCP port |
| `http` | HTTP traffic |
| `http.request.method == "POST"` | POST requests |
| `dns` | DNS traffic |
| `tcp.flags.syn == 1 && tcp.flags.ack == 0` | SYN packets |
| `tcp contains "password"` | payload string match |
| `ftp \|\| ftp-data` | FTP control+data |
| `eth.addr == aa:bb:cc:dd:ee:ff` | by MAC |

## Capture filters (BPF syntax, set before capture starts)
```
host 10.0.0.5
port 443
tcp and not port 22
net 192.168.1.0/24
```

## tshark one-liners
```bash
# Extract HTTP requests
tshark -r capture.pcap -Y http.request -T fields -e http.host -e http.request.uri

# Extract DNS queries
tshark -r capture.pcap -Y dns.flags.response==0 -T fields -e dns.qry.name

# Follow a TCP stream
tshark -r capture.pcap -q -z follow,tcp,ascii,0

# Extract credentials from unencrypted protocols (ftp/http basic auth)
tshark -r capture.pcap -Y 'ftp.request.command=="PASS" || http.authorization' -T fields -e ftp.request.arg -e http.authorization

# Export objects (files transferred over HTTP)
tshark -r capture.pcap --export-objects http,./extracted/
```

## GUI shortcuts
| Action | Shortcut |
|---|---|
| Apply filter | Enter |
| Follow TCP/UDP stream | right-click packet → Follow |
| Statistics → Conversations | see all host pairs + bytes |
| Statistics → Protocol Hierarchy | traffic breakdown by protocol |
| File → Export Objects | pull files out of HTTP/SMB/etc. |

## Recipes
```bash
# Live capture filtered to one host, rotate files every 100MB
sudo tshark -i eth0 -f "host 10.0.0.5" -b filesize:100000 -w cap.pcap

# Pull all JPEGs seen over HTTP
tshark -r cap.pcap --export-objects http,out/ && find out/ -iname '*.jpg'
```

## Gotchas / OPSEC
- Live capture needs root or `CAP_NET_RAW`/`CAP_NET_ADMIN` (or add your user to the `wireshark` group).
- Capturing on a switched network only sees your own traffic unless you're on a mirror port or doing ARP spoofing (see `qyvora/toha3ee.md` `arp.spoof`).

## See also
- https://www.wireshark.org/docs/
- `network/tcpdump.md`, `qyvora/toha3ee.md`
