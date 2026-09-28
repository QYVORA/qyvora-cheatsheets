# Git Cheat Sheet
> Version control · General · `apt install git`

---

## Quick start
```bash
git init
git clone <url>
git add . && git commit -m "message"
git push origin main
```

## Everyday commands
| Task | Command |
|---|---|
| Status | `git status` |
| Stage | `git add <file>` / `git add .` |
| Commit | `git commit -m "msg"` |
| Push | `git push origin <branch>` |
| Pull | `git pull origin <branch>` |
| New branch | `git checkout -b <branch>` |
| Switch branch | `git checkout <branch>` |
| Merge | `git merge <branch>` |
| Log | `git log --oneline --graph --all` |
| Diff | `git diff` / `git diff --staged` |
| Stash | `git stash` / `git stash pop` |

## Undo / fix mistakes
| Task | Command |
|---|---|
| Unstage a file | `git restore --staged <file>` |
| Discard local changes | `git restore <file>` |
| Amend last commit | `git commit --amend -m "new msg"` |
| Undo last commit, keep changes | `git reset --soft HEAD~1` |
| Undo last commit, discard changes | `git reset --hard HEAD~1` |
| Revert a pushed commit safely | `git revert <hash>` |
| Recover a "lost" commit | `git reflog` then `git checkout <hash>` |

## Branch & remote management
```bash
git branch -a                       # list all branches
git branch -d <branch>              # delete local branch
git push origin --delete <branch>   # delete remote branch
git remote -v                       # list remotes
git remote set-url origin <url>     # change remote URL
```

## Rebase & cleanup
```bash
git rebase -i HEAD~5                # interactive rebase, squash/reorder last 5 commits
git rebase main                     # rebase current branch onto main
git cherry-pick <hash>              # apply a specific commit onto current branch
```

## Recipes
```bash
# Clean up before pushing: squash all commits into one
git reset --soft $(git merge-base main HEAD)
git commit -m "Consolidated feature"

# Find which commit introduced a bug
git bisect start
git bisect bad
git bisect good <known-good-hash>
# git will checkout commits for you to test; mark each `git bisect good/bad`

# Search commit history for a string (e.g. leaked secret)
git log -p -S "API_KEY" --all
```

## .gitignore essentials
```
node_modules/
*.log
.env
__pycache__/
*.pyc
```

## Gotchas / OPSEC
- `git log -p -S` and `git log --all` can surface secrets committed and later "removed" — they're still in history until you rewrite it (`git filter-repo` or BFG Repo-Cleaner).
- `git reset --hard` and `git push --force` are destructive — confirm you're not discarding a teammate's unpushed work.

## See also
- https://git-scm.com/docs
- `general/tmux.md`
