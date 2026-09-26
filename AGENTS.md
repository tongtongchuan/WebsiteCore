## Development habits

- Unless explicitly instructed otherwise, PRs must be reviewed by the user before being opened.

## Agent skills

### Worktree setup

- After creating a worktree, run `~/workspace/setup-agent-worktree.py` from that worktree before starting work, then read its `AGENTS.md` and referenced configuration.
- Personal agent configuration and task records are maintained on the fork remote's `agent-config` branch. The setup script fetches that branch and installs `AGENTS.md`, `docs/agents/`, and `.scratch/` as locally ignored files; business branches keep their existing history and index.
- Keep configuration and task-record edits local until explicitly asked to publish them to `fork/agent-config`. These updates belong on that branch, separate from business commits and PRs.
- Each worktree receives its own copy of `.scratch/`. Re-running setup syncs unchanged files and stops on conflicting local edits; reconcile those edits before retrying. Run implementation skills on the task branch, preserving the main worktree's branch and uncommitted changes. The PR review rule above also applies to draft PRs created by skills.

### Issue tracker

Issues live as local markdown files under `.scratch/` in this repo. See `docs/agents/issue-tracker.md`.

### Triage labels

Default five-role vocabulary, label strings equal to their names. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context (`CONTEXT.md` + `docs/adr/` at the repo root). See `docs/agents/domain.md`.
