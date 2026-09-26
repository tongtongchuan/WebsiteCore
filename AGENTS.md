## Development habits

- Use `AGENTS.md` and its referenced configuration for agent instructions. Ignore `CLAUDE.md` when implementing or reviewing changes, including instructions inherited through skills or sub-agents.
- Unless explicitly instructed otherwise, PRs must be reviewed by the user before being opened.

## Agent skills

### Worktree setup

- After creating a worktree, run `~/workspace/setup-agent-worktree.py` from that worktree before starting work, then read its `AGENTS.md` and referenced configuration.
  Usually the script will run by the human or you can't see this doc.
- Personal agent configuration, domain documents, and task records are maintained on the fork remote's `agent-config` branch. Setup installs `AGENTS.md`, `docs/agents/`, and any existing `CONTEXT.md`, `CONTEXT-MAP.md`, and `docs/adr/` locally, and links every worktree's `.scratch/` to the repository's shared Git directory at `agent-shared/.scratch`. These paths are locally ignored; business branches keep their existing history and index.
- After changing any of these synced documents, run `~/workspace/setup-agent-worktree.py --publish --message "Describe the document changes"` before finishing the task. This commits the documents with a separate index and pushes directly to `fork/agent-config`; document publication is authorized by this rule. Report any failed push or conflict as unfinished work. Keep these updates separate from business commits and PRs.
- All worktrees share live `.scratch/` files. Claim tickets before working on them and coordinate edits to the same file. Re-running setup preserves local task edits when the remote version is unchanged and stops if both versions changed differently. Migration retains old directories as ignored `.scratch.agent-backup-*` backups.
- Run implementation skills on the task branch, preserving the main worktree's branch and uncommitted changes. The PR review rule above also applies to draft PRs created by skills.

### Issue tracker

Issues live as local markdown files under `.scratch/` in this repo. See `docs/agents/issue-tracker.md`.

### Triage labels

Default five-role vocabulary, label strings equal to their names. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context (`CONTEXT.md` + `docs/adr/` at the repo root). See `docs/agents/domain.md`.
