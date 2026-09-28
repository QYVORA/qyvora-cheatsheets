# GDB / pwndbg Cheat Sheet
> Debugging & binary exploitation · Reversing · `apt install gdb` + https://github.com/pwndbg/pwndbg

---

## Quick start
```bash
gdb ./binary
gdb -p <pid>                # attach to running process
(gdb) run arg1 arg2
```

## Core commands
| Command | Purpose |
|---|---|
| `break <loc>` / `b <loc>` | breakpoint at function/address/`file:line` |
| `run` / `r` | start execution |
| `continue` / `c` | resume |
| `next` / `n` | step over |
| `step` / `s` | step into |
| `finish` | run until current function returns |
| `info registers` / `i r` | show registers |
| `x/20xw $rsp` | examine 20 hex words at RSP |
| `disassemble` / `disas` | disassemble current function |
| `bt` | backtrace |
| `info breakpoints` | list breakpoints |
| `watch <var>` | break on write to a variable |

## pwndbg additions
| Command | Purpose |
|---|---|
| `context` | full register/disasm/stack view (auto-shown at each stop) |
| `vmmap` | memory mappings with permissions |
| `checksec` | binary protections (NX, PIE, canary, RELRO) |
| `heap` | heap chunk overview (glibc) |
| `bins` | tcache/fastbin/unsorted bin contents |
| `search -t string "text"` | search memory for a pattern |
| `telescope $rsp 20` | dereference chain view of stack |
| `cyclic 200` | generate a De Bruijn pattern for offset-finding |
| `cyclic -l 0x6161616c` | find offset of a crash value in the pattern |
| `got` | GOT entries and their resolution state |
| `rop` | ROP gadget search |

## Recipes
```bash
# Find buffer overflow offset
(gdb) run $(python3 -c 'print("A"*200)')
(gdb) cyclic 200
# after crash:
(gdb) cyclic -l $(gdb -batch -ex 'print $rip' ./binary | cut -d' ' -f3)

# Check binary protections before exploiting
(gdb) checksec

# Find ROP gadgets
(gdb) rop --grep "pop rdi"

# Patch a breakpoint to auto-continue with commands
(gdb) break *0x401234
(gdb) commands
> print $rax
> continue
> end
```

## GDB scripting (`.gdbinit` / `-x script.gdb`)
```
break main
commands
  printf "hit main\n"
  continue
end
run
```

## Gotchas / OPSEC
- ASLR is on by default for `run` inside gdb only if the OS has it enabled; `disable-randomization` toggles gdb's own re-exec behavior, not system-wide ASLR.
- Ensure `set follow-fork-mode child` if debugging a program that forks and you want to trace the child.

## See also
- https://github.com/pwndbg/pwndbg
- `reversing/radare2.md`, `qyvora/aksum.md`, `qyvora/sekhmet.md` (crash triage of fuzzing finds)
