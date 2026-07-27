# flow

A collection of workflow utilities for working with git and GitHub, mostly packaged as [Claude Code](https://claude.ai/code) skills.

## What's here

### Skills (`.claude/skills/`)

- **scommit** — a no-frills commit: diff the working tree, derive a message, `git commit -am`.
- **open-pr** — take finished-but-uncommitted work, create a branch, commit, push, and open a GitHub pull request.
- **pr-review** — collect and triage feedback on a GitHub PR (Copilot reviews, CI failures, human comments), classifying each item before fixing anything.

The skills are invoked explicitly as slash commands (`/scommit`, `/open-pr`, `/pr-review`) from within Claude Code.

### Scripts (`scripts/`)

- **pr-feedback.sh** — gathers GitHub PR feedback for the current branch (or a given PR number) into a single report. Used by the pr-review skill; requires `gh`, `git`, and `jq`.
