---
allowed-tools: Bash(git diff:*), Bash(git status:*), Bash(git commit:*), Bash(git log:*)
description: Simple commit — diff, derive a message, git commit -am
---

# Simple Commit (scommit)

A no-frills commit. No pre-commit checks, no staging decisions, no splitting.
Just look at the changes, write a good message, and commit all tracked changes
with `git commit -am`.

## Process:
1. Review the changes: !`git status --short` and !`git diff`
2. Derive a concise commit message from the diff (imperative mood, explain the *what*; add a short body only if the change is non-obvious)
3. Run `git commit -am "<message>"`

## Notes:
- `git commit -am` stages and commits **tracked** files only — new untracked files are not included. That's expected for this command; use `/commit` if you need to stage new files.
- Match the style of recent commits (!`git log --oneline -10`).
- Do NOT prefix the commit message with emojis — plain text only, even if recent commits use them.
- Do NOT add a Claude co-authorship footer.
- Do NOT run lint/build/test or other pre-commit checks — that's the whole point of this being the *simple* commit.
