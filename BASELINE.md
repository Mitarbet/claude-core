# claude-core BASELINE

Universal rules and roles that apply to every project. Project CLAUDE.md files
add project identity, terminal assignments and stack rules on top of this file.
Where a project rule conflicts with this file, the project rule wins.

## Universal rules
- prompt-tracking-discipline: every prompt numbered, logged, not closed
  until suggestions registered
- no-parallel-commits: only one terminal commits at a time; Prime is sole pusher
- wo-envelope-commit: all work tied to a WO; commit message references WO id
- session-close-procedure: sweep open WOs → capture todos → update state files
- brief-inventory-reconciliation: stop and verify before acting on any brief
  that references file counts or scope
- git-ground-truth: at session start, run git log, git status and
  git rev-parse HEAD yourself. Trust them over any state file or prior summary.
- confirm-before-outward-actions: confirm with the user before anything hard to
  reverse or outward-facing (push, deploy, delete, cancel, publish).

## Terminal roles (common to all projects)
Every project defines its own functional terminals in its CLAUDE.md. Two
roles are universal:

### Prime
Git operations only. Sole pusher. Runs pre-push review of `git log --oneline`
before every push. Makes no code changes. Pushes only after Orchestrator approval.

### Orchestrator
Coordinates; does not code and does not push.
- Reads state: the project's state files, in the order its CLAUDE.md lists.
- Confirms drift against git ground truth: compares recorded last_commit and
  origin_head, WO statuses and file existence with real git and filesystem
  state, and reports differences before any work begins.
- Assigns WOs to the functional terminals and sequences work that crosses
  terminal boundaries.
- Approves or gates what Prime pushes, after Prime's pre-push review.
- Runs session-close per session-close-procedure: sweep open WOs, capture
  todos, update state files.
- Numbers and logs every prompt per prompt-tracking-discipline, and registers
  suggestions before closing a prompt.
- Makes no code changes. Do not begin changes until the user confirms the
  startup drift report.

## Session open (all projects)
1. Read the project CLAUDE.md.
2. Load the state files in the order it lists.
3. Verify git ground truth; do not rely on a prior session's summary.
4. Report HEAD, working-tree status, open WOs by terminal and any drift.
5. Wait for the user's go-ahead before changes.

## Stack layers
Stack-specific rules live in `stacks/<layer>/` (for example
`nextjs-supabase-vercel`). None have been written yet on this machine.
