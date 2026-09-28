# Hashcat Cheat Sheet
> GPU password cracking · Passwords · `apt install hashcat`

---

## Quick start
```bash
hashcat -m 0 -a 0 hashes.txt rockyou.txt          # MD5, dictionary attack
hashcat -m 1000 -a 0 hashes.txt rockyou.txt       # NTLM
```

## Common hash modes (`-m`)
| Mode | Hash type |
|---|---|
| 0 | MD5 |
| 100 | SHA1 |
| 1400 | SHA256 |
| 1700 | SHA512 |
| 1000 | NTLM |
| 3000 | LM |
| 500 | md5crypt (Unix) |
| 1800 | sha512crypt (Unix) |
| 5600 | NetNTLMv2 |
| 13100 | Kerberos 5 TGS-REP (kerberoast) |
| 18200 | Kerberos 5 AS-REP (ASREPRoast) |
| 22000 | WPA-PBKDF2 (WPA/WPA2 handshake, hashcat capture format) |
| 3200 | bcrypt |

`hashcat -h | grep -i <name>` to find a mode by name; `hashcat --identify hashes.txt` to guess format.

## Attack modes (`-a`)
| Mode | Type |
|---|---|
| 0 | dictionary (straight) |
| 1 | combinator (word1+word2) |
| 3 | brute-force / mask |
| 6 | dictionary + mask (hybrid) |
| 7 | mask + dictionary (hybrid) |

## Mask attack examples
```bash
hashcat -m 0 -a 3 hashes.txt ?u?l?l?l?l?d?d           # Aaaaa99 pattern
hashcat -m 0 -a 3 hashes.txt --increment -1 ?l?d ?1?1?1?1?1?1
```
Charsets: `?l` lower, `?u` upper, `?d` digit, `?s` symbol, `?a` all.

## Rules
```bash
hashcat -m 0 -a 0 hashes.txt rockyou.txt -r rules/best64.rule
hashcat -m 0 -a 0 hashes.txt rockyou.txt -r rules/rockyou-30000.rule
```

## Recipes
```bash
# Kerberoast hash cracking (from Impacket GetUserSPNs.py output)
hashcat -m 13100 -a 0 kerberoast.txt rockyou.txt -r rules/best64.rule

# WPA2 handshake crack
hcxpcapngtool -o hash.hc22000 capture.pcapng
hashcat -m 22000 -a 0 hash.hc22000 rockyou.txt

# Resume an interrupted session
hashcat --session mysess -m 1000 -a 0 hashes.txt rockyou.txt
hashcat --session mysess --restore

# Show cracked results
hashcat -m 1000 hashes.txt --show
```

## Common flags
| Flag | Meaning |
|---|---|
| `-O` | optimized kernels (shorter password length limits) |
| `-w 3` | workload profile (1-4, higher = more aggressive) |
| `--force` | bypass hardware warnings |
| `--status --status-timer=10` | periodic progress |
| `-o cracked.txt` | output file |
| `--username` | strip username field from input |

## Gotchas / OPSEC
- `-O` caps max password length per algorithm — drop it if you suspect long passphrases.
- Benchmark first: `hashcat -b -m <mode>` to estimate crack time before committing GPU-hours.

## See also
- https://hashcat.net/wiki/
- `passwords/john.md`, `passwords/hydra.md`, `qyvora/sundiata.md`
