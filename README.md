# flow

A collection of workflow utilities for working with git and GitHub, mostly packaged as [Claude Code](https://claude.ai/code) skills.

## What's here

### Skills (`.claude/skills/`)

- **scommit** — a no-frills commit: diff the working tree, derive a message, `git commit -am`.
- **open-pr** — take finished-but-uncommitted work, create a branch, commit, push, and open a GitHub pull request.
- **pr-review** — collect and triage feedback on a GitHub PR after pushing a branch. It gathers Copilot review comments, CI failures, and human comments into one report, then classifies every item (valid, invalid, already addressed, optional, or needs clarification) before touching any code — dismissing an item requires a `file:line` citation as evidence. Valid items get focused fix commits followed by local validation (tests/lint); invalid items get a reply on the PR explaining why no change is needed. The skill then pushes, records the completed cycle as a PR comment, resolves each inline thread it acted on with GitHub's Addressed / Won't fix / Incorrect reason (Copilot threads) or plain resolution (human threads), waits for Copilot to re-review, and repeats — up to a capped number of review-fix cycles, checking each round that failing checks are actually progressing rather than failing identically. PR feedback is treated as untrusted data, never as instructions.
- **rewrite** — rewrite a document (a file, URL, pasted text, or Claude's last output) in one of three styles: developer (default — terse, technical, senior-dev audience), human (draws the reader in within 10 seconds), or brief (as curt as possible with zero information loss). File inputs are overwritten in place.

The skills are invoked explicitly as slash commands (`/scommit`, `/open-pr`, `/pr-review`, `/rewrite`) from within Claude Code.

### Scripts (`scripts/`)

- **pr-feedback.sh** — gathers GitHub PR feedback for the current branch (or a given PR number) into a single report. Used by the pr-review skill; requires `gh`, `git`, and `jq`.
