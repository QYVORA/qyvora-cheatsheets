# QYVORA Cheat Sheets

A collection of concise, practical cheat sheets: the 12 QYVORA open-source security frameworks, plus popular offensive-security tools organized by domain.

Every sheet follows [`TEMPLATE.md`](TEMPLATE.md) — Quick start, Most-used commands, Common flags, Recipes, Output/reporting, Gotchas/OPSEC, See also.

> **Authorized use only.** Every tool here is for use against systems you own or are explicitly authorized to test.

---

## QYVORA tools (12)

| Tool | Domain | Sheet |
|---|---|---|
| Anansi | Web attack-surface intelligence | [`qyvora/anansi.md`](qyvora/anansi.md) |
| TOHA3EE | Network/MITM/post-ex (73 modules) | [`qyvora/toha3ee.md`](qyvora/toha3ee.md) |
| Jabari | Android security assessment | [`qyvora/jabari.md`](qyvora/jabari.md) |
| Aksum | Binary security / reverse engineering | [`qyvora/aksum.md`](qyvora/aksum.md) |
| Shaka | Active Directory / Windows | [`qyvora/shaka.md`](qyvora/shaka.md) |
| Nzinga | OSINT / intelligence | [`qyvora/nzinga.md`](qyvora/nzinga.md) |
| Mansa | Wireless (WLAN) security | [`qyvora/mansa.md`](qyvora/mansa.md) |
| Sekhmet | Fuzzing / vuln discovery | [`qyvora/sekhmet.md`](qyvora/sekhmet.md) |
| Amanirenas | Mobile app (iOS/IPA) analysis | [`qyvora/amanirenas.md`](qyvora/amanirenas.md) |
| Imhotep | Cloud snapshot analysis | [`qyvora/imhotep.md`](qyvora/imhotep.md) |
| Sundiata | Identity & access assessment | [`qyvora/sundiata.md`](qyvora/sundiata.md) |
| Timbuktu | Incident response / forensics | [`qyvora/timbuktu.md`](qyvora/timbuktu.md) |
| Kush | Malware sample analysis | [`qyvora/kush.md`](qyvora/kush.md) |

## Popular tools by category

| Category | Sheets |
|---|---|
| Recon | [`recon/nmap.md`](recon/nmap.md) |
| Web | [`web/burp-suite.md`](web/burp-suite.md) · [`web/ffuf.md`](web/ffuf.md) · [`web/sqli-cheatsheet.md`](web/sqli-cheatsheet.md) |
| Network | [`network/netcat.md`](network/netcat.md) · [`network/wireshark-tshark.md`](network/wireshark-tshark.md) · [`network/bettercap.md`](network/bettercap.md) |
| Exploitation | [`exploitation/metasploit.md`](exploitation/metasploit.md) |
| Passwords | [`passwords/hashcat.md`](passwords/hashcat.md) · [`passwords/hydra.md`](passwords/hydra.md) |
| Post-exploitation | [`post-exploitation/linux-privesc.md`](post-exploitation/linux-privesc.md) |
| AD / Windows | [`ad-windows/netexec.md`](ad-windows/netexec.md) · [`ad-windows/bloodhound.md`](ad-windows/bloodhound.md) · [`ad-windows/impacket.md`](ad-windows/impacket.md) |
| Reversing | [`reversing/gdb-pwndbg.md`](reversing/gdb-pwndbg.md) |
| OSINT | [`osint/theharvester-sherlock.md`](osint/theharvester-sherlock.md) |
| Wireless | [`wireless/aircrack-ng.md`](wireless/aircrack-ng.md) |
| General | [`general/tmux.md`](general/tmux.md) · [`general/git.md`](general/git.md) |

Empty category folders (`mobile/`, `cloud/`, `forensics/`) are scaffolded for tools like Frida, MobSF, ScoutSuite, Prowler, Volatility, and Autopsy — add sheets there as needed using `TEMPLATE.md`.

## Contributing
1. Copy `TEMPLATE.md` into the right category folder.
2. For a **QYVORA tool**: pull commands from the tool's own `--help`, `capabilities` output, and `docs/`. Don't invent flags.
3. For a **popular tool**: write original wording — don't copy-paste from other cheat sheets (copyright). Link to official docs in "See also".
4. Cross-link related sheets (QYVORA framework ↔ the popular tools covering the same ground) so readers can jump between them.
5. Update the tables above when you add a file.
