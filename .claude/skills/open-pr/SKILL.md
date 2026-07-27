---
description: >-
  Take finished-but-uncommitted work, create a branch for it, commit, push, and
  open a GitHub pull request. Use after the work is done, before anything is
  committed. GitHub and `gh` only. Pairs with pr-review for handling the
  feedback that comes back.
# disable-model-invocation: this skill commits, pushes a branch, and opens a
# public PR; it must only run as an explicit /open-pr, never on a fuzzy
# description match.
disable-model-invocation: true
# git push requires the literal "-u origin" prefix so bare force-pushes and
# arbitrary refspecs don't match; the trailing wildcard is only for the branch
# name. Never pass --force here.
allowed-tools: >-
  Bash(git status*) Bash(git diff*) Bash(git log*) Bash(git rev-parse*)
  Bash(git branch --show-current) Bash(git switch -c *)
  Bash(git add*) Bash(git commit*) Bash(git push -u origin *)
  Bash(gh repo view*) Bash(gh pr create*) Bash(gh pr view*) Bash(gh pr list*)
  Read Grep Glob
argument-hint: "[branch-name (optional)] [--draft] [--base <branch>]"
---

## Open a PR from pending work

Turn the uncommitted work in this repository into a branch with a commit and an
open pull request.

### Preflight

1. Run `git status` and `git diff` to see what is pending. If there is nothing
   to commit and the current branch has no unpushed commits, stop and say so.
2. Determine the default branch:
   `gh repo view --json defaultBranchRef -q .defaultBranchRef.name`
   (fall back to `git rev-parse --abbrev-ref origin/HEAD` if `gh` fails).
   The PR base is that branch unless the user passed `--base`.
3. Check the current branch with `git branch --show-current`:
   - **On the default branch with uncommitted changes** — the normal case;
     continue.
   - **Already on a feature branch** — do not create another branch. Commit,
     push, and open the PR from here (first check `gh pr list --head <branch>`
     for an existing PR; if one exists, just commit and push, then report its
     URL).
   - **On the default branch with local commits ahead of origin** — those
     commits would need to be moved off the default branch, which this skill
     does not do. Stop and ask the human how to proceed.
4. Scan the untracked files in `git status` for anything that should not be
   committed (secrets, `.env`, credentials, large build artifacts). If found,
   leave it out and tell the user; if unsure, ask.

### Branch

Use `$1` as the branch name if the user provided one. Otherwise derive a short
kebab-case name from the substance of the change, matching any convention
visible in `git log` (e.g. `feat/…`, `fix/…` prefixes if the repo uses them).

```bash
git switch -c <branch-name>
```

### Commit

Stage the pending work with `git add` (explicit paths — never `git add .` when
step 4 flagged files). Write the commit message from the diff, following the
style of recent `git log` messages (conventional-commit prefixes, tense,
capitalization). Make one commit unless the diff clearly contains unrelated
changes, in which case make focused commits.

### Push and open the PR

```bash
git push -u origin <branch-name>
gh pr create --base <base> --title "<title>" --body "<body>"
```

Pass `--draft` to `gh pr create` if the user passed `--draft`.

- **Title**: match the primary commit message's style; keep it under ~70
  characters.
- **Body**: a `## Summary` section (1–3 bullets on what changed and why) and a
  `## Test plan` section (how the change was or should be verified). Do not
  pad with boilerplate.

### Report

End by giving the user the PR URL, the branch name, and a one-line summary of
what was committed. Suggest running `/pr-review <pr-number>` once CI and
reviewers have had a pass.
