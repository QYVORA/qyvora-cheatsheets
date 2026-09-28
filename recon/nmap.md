# Nmap Cheat Sheet
> Network discovery & port scanning · Recon · `apt install nmap`

---

## Quick start
```bash
nmap -sn 192.168.1.0/24               # host discovery (ping sweep)
nmap -p- -T4 target.com               # all 65535 ports
nmap -sV -sC -p 22,80,443 target.com  # version + default scripts on common ports
```

## Most-used commands
| Task | Command |
|---|---|
| Fast top-1000 scan | `nmap target.com` |
| All ports | `nmap -p- target.com` |
| Service/version detect | `nmap -sV target.com` |
| OS detection | `nmap -O target.com` |
| Default NSE scripts | `nmap -sC target.com` |
| Aggressive (all of the above) | `nmap -A target.com` |
| UDP scan | `nmap -sU --top-ports 20 target.com` |
| Stealth SYN scan | `sudo nmap -sS target.com` |
| Skip ping (host known up) | `nmap -Pn target.com` |
| Output all formats | `nmap -oA scan target.com` |

## Common flags
| Flag | Meaning |
|---|---|
| `-T0`..`-T5` | timing template (0=paranoid, 5=insane) |
| `-p <ports>` | specific ports/ranges |
| `--top-ports N` | scan N most common ports |
| `-v / -vv` | verbosity |
| `-oN/-oX/-oG/-oA` | normal/XML/grepable/all output |
| `--script <name/category>` | run NSE script(s) |
| `--script-args` | pass args to scripts |
| `-e <iface>` | interface |
| `-D RND:10` | decoy scan |
| `-f` | fragment packets |
| `--min-rate N` | packets/sec floor |

## Recipes
```bash
# Full TCP + top UDP, then targeted NSE vuln scripts
nmap -p- -T4 -oA full target.com
nmap -sU --top-ports 50 -oA udp target.com
nmap -sV --script vuln -p 22,80,443,445 target.com

# Web-focused
nmap -p80,443 --script http-title,http-headers,http-methods target.com

# SMB enumeration
nmap -p445 --script smb-os-discovery,smb-enum-shares,smb-enum-users target.com

# Grep live hosts from a subnet, feed into further scans
nmap -sn 10.0.0.0/24 -oG - | awk '/Up$/{print $2}' > live.txt
nmap -iL live.txt -sV -oA followup
```

## Output & reporting
`-oA basename` writes `.nmap` `.xml` `.gnmap` together — feed `.xml` into `xsltproc` for an HTML report, or into tools like `eyewitness`/Metasploit's `db_import`.

## Gotchas / OPSEC
- `-sS` (SYN scan) needs root/`CAP_NET_RAW`.
- Aggressive timing (`-T4/-T5`) is loud — drop to `-T2` for stealthier engagements.
- Some IDS/IPS fingerprint the default NSE script set — `--script-args` and custom timing help evade naive detection, but never rely on this for authorization boundaries.

## See also
- https://nmap.org/book/man.html
- `recon/httpx.md`, `web/ffuf.md`, `qyvora/anansi.md`
