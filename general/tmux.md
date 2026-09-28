# Tmux Cheat Sheet
> Terminal multiplexer · General · `apt install tmux`

Default prefix: `Ctrl+b` (written as `C-b` below)

---

## Quick start
```bash
tmux new -s work            # start a named session
tmux attach -t work         # reattach later
tmux ls                     # list sessions
```

## Session management
| Action | Command |
|---|---|
| New session | `tmux new -s <name>` |
| Detach | `C-b d` |
| Reattach | `tmux attach -t <name>` |
| List sessions | `tmux ls` |
| Kill session | `tmux kill-session -t <name>` |
| Rename session | `C-b $` |

## Windows (tabs)
| Action | Key |
|---|---|
| New window | `C-b c` |
| Next/prev window | `C-b n` / `C-b p` |
| Go to window N | `C-b <N>` |
| Rename window | `C-b ,` |
| List windows | `C-b w` |
| Close window | `C-b &` |

## Panes (splits)
| Action | Key |
|---|---|
| Split vertical | `C-b %` |
| Split horizontal | `C-b "` |
| Switch pane | `C-b <arrow>` |
| Close pane | `C-b x` |
| Zoom pane (toggle fullscreen) | `C-b z` |
| Swap panes | `C-b {` / `C-b }` |
| Resize pane | `C-b : resize-pane -D/-U/-L/-R 10` |

## Copy mode
```
C-b [           # enter copy mode
Space           # start selection (vi mode)
Enter           # copy selection
C-b ]           # paste
```

## Recipes
```bash
# Run a long scan detached, check on it later
tmux new -s scan -d 'nmap -p- -T4 target.com -oA fullscan'
tmux attach -t scan

# Send a command into a named session without attaching
tmux send-keys -t scan 'echo done' Enter
```

## ~/.tmux.conf essentials
```
set -g mouse on
set -g history-limit 10000
bind r source-file ~/.tmux.conf \; display "Reloaded"
```

## Gotchas
- Detaching (`C-b d`) leaves the session running — great for long scans over SSH that must survive disconnects.
- Nested tmux (tmux inside tmux over SSH) needs `C-b C-b <key>` to send the inner session's prefix.

## See also
- https://github.com/tmux/tmux/wiki
- `general/vim.md`
