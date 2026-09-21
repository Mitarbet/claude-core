# claude-core BASELINE

Universal rules and roles that apply to every project. Project CLAUDE.md files
add project identity, terminal assignments and stack rules on top of this file.
Where a project rule conflicts with this file, the project rule wins.

## Working-tree topology

In a multi-terminal project, assume every terminal shares one working directory
and one `.git` object database — not independent clones. A sibling terminal's
uncommitted edit really does appear in your working tree; a destructive git
operation really can destroy a sibling's work. This assumption underlies the
git-safety rules below; if a project's environment genuinely gives each
terminal its own clone, its CLAUDE.md should say so explicitly.

## Universal rules

### Process discipline

- **confirm-before-outward-actions**: confirm with the user before anything
  hard to reverse or outward-facing (push, deploy, delete, cancel, publish).
- **git-ground-truth**: at session start, run `git log`, `git status` and
  `git rev-parse HEAD` yourself. Trust them over any state file or prior
  summary.

### Git safety

These rules apply when more than one terminal is active in a project (see
Working-tree topology). In a single-terminal session, skip
git-identity-per-terminal, non-prime-fetch-on-prompt, no-parallel-commits,
pre-rebase-discipline and session-open-inherited-mods; the rest still apply.
The default branch is whatever the repo uses (`main`, `master`); substitute it
for `<default-branch>` below.

- **git-identity-per-terminal**: every terminal sets `git config user.name
  "<Terminal>"` at session open (role name, not a human name or lowercase
  string; a project's CLAUDE.md may override this, for example to use one
  human author) and uses the per-command override on every commit and amend —
  `git -c user.name="<Terminal>" commit <path> -m "..."` — rather than relying
  on `.git/config`, which is shared and can be overwritten by a sibling
  terminal between your last check and your commit. Verify `git config
  user.name` before a commit as a fallback if the `-c` override is ever
  skipped. Do not rebase-reword already-pushed commits to fix historical
  misattribution — accept it as record; prevention (the `-c` override) is the
  only real remedy.
- **non-prime-fetch-on-prompt**: every non-Prime terminal runs `git fetch
  origin` as the first command of every new prompt — before any read, write,
  or commit — and logs the output even when empty. Run it a second time,
  plus `git log origin/<default-branch>..HEAD --oneline`, immediately before any
  destructive operation (`reset --hard`, `rebase`, `commit --amend`, `push
  --force`, `branch -D`). Prime is exempt from the per-prompt fetch (its
  fetch discipline is push-scoped — see prime-push-all-unpushed below) but
  not from the pre-destructive-op fetch.
- **no-parallel-commits**: only one terminal commits (or runs `git add`,
  `rebase`, `revert`, or any other index-lock operation) at a time. The
  orchestrator sequences commit windows; a terminal announces readiness,
  waits for the signal, commits, and reports the SHA before the next
  terminal proceeds. Use the one-shot path form — `git commit <path>
  -m "..."` — never bare `git commit` while other terminals are active; run
  `git diff --cached --stat` first to confirm nothing unexpected is staged.
  Verify every commit with `git log -1 --oneline` immediately after (new SHA,
  correct subject). Before any `git commit --amend`, verify `git rev-parse
  HEAD` matches the SHA you intend to amend — HEAD can move between when a
  target is named and when the amend runs.
- **prime-push-all-unpushed**: before every push, Prime lists every commit
  about to publish (`git log origin/<default-branch>..HEAD --oneline` and `git
  rev-list --count`), reports both plus an explicit "I have reviewed each
  commit; none are unexpected" to the orchestrator, and waits for approval
  naming the expected count *and* SHA list before running `git push`. No
  exceptions for routine or doc-only commits. Any amend after approval
  invalidates the SHA list and requires re-approval. Non-Prime terminals
  never run `git push` — publishing every unpushed commit including ones
  Prime hasn't yet had reviewed defeats this entire discipline.
- **pre-rebase-discipline**: before any rebase, fetch and pre-count the
  unpushed stack (`git fetch origin` then `git log origin/<default-branch>..HEAD
  --oneline`); if the count doesn't match what you expected, stop and
  report rather than rebasing anyway. Use an explicit SHA (`git rebase -i
  <sha>^`), never a moving `HEAD~N` reference, whenever another terminal may
  have committed since your last fetch. Before Prime rebases, the
  orchestrator confirms every other active terminal has committed or
  stashed its working-tree edits — a rebase's replay silently overwrites
  uncommitted sibling work with no warning from git.
- **session-open-inherited-mods**: at session open, and again at any prompt
  boundary after an idle gap (>1 prompt cycle during which siblings may have
  acted), run `git status --short` and `git log origin/<default-branch>..HEAD
  --oneline` before accepting new work. If the tree is dirty with a
  modification you don't own, stash it with a tagged name (e.g.
  `{terminal}-inherited-hold-{context}`) and report the stash to the
  orchestrator in the same turn — not deferred to session close. Drop a
  stash once you confirm the owning terminal committed its own version;
  otherwise carry it forward.
- **yaml-edit-reread**: before editing a shared state/registry file, re-read
  it from disk — never reuse a Read from earlier in the session, even your
  own from a few tool calls ago, since a sibling terminal may have committed
  changes in between. A `Grep` result does not satisfy this — always follow
  it with a `Read` of the target section before an `Edit`.

### WO / issue lifecycle

- **wo-id-on-every-commit**: all work is tied to a WO, and every commit message
  references its WO id. If work has no WO, register one before committing. A
  project's own commit-format rule may define the exact format.
- **wo-envelope-commit**: for any work order shipping more than ~3 commits,
  close it with a final envelope commit (`meta: WO-XXX envelope — <scope>`)
  listing the implementation commits it closes. Not a squash, not mandatory
  for small WOs — it's a lightweight audit anchor for push review.
- **wo-completion-criteria**: every WO gets a concrete, binary
  `completion_criteria` list at registration time (not written
  retroactively at close). Each bullet must be independently verifiable — a
  file exists, a test passes, a commit landed — not "works correctly." A
  bullet is only "ticked" when annotated with concrete evidence (a commit
  SHA, file path, migration number, CI run, test path) — a bare checkmark or
  "done" is an assertion, not a tick, and doesn't count. Closure metadata
  (`status: complete`, completion date, implementing commit) is written only
  in a separate pass after the implementing commit actually exists — never
  at registration time as a placeholder.
- **no-unlogged-suggestions**: every suggestion, recommendation, or "worth
  considering" raised during a session is converted immediately into a
  registry entry — a WO, an ISS, a rule, a deferred-consideration note, or a
  memory file — not left to live only in chat or a status-report narrative.
  If it's worth saying, it's worth logging, right then, not at session
  close.
- **brief-inventory-reconciliation**: before acting on a brief's explicit
  count or any concrete identifier it pre-declares (a file count, column
  name, table name, migration number, row count), verify it against live
  state first and report the actual result. If the brief's claim diverges
  from reality, stop and report the discrepancy — don't proceed on a stale
  snapshot. The cost of the check is seconds; the cost of editing a
  nonexistent file or column is a recovery exercise.

### Orchestration

- **prompt-tracking-discipline**: at session open, initialize a live prompt
  tracker (`# | P# | Terminal | Prompt Summary | Submitted? | Status`).
  Every prompt issued to any terminal — including continuations — gets the
  next sequential P#, never reused, resetting to P1 each session. States:
  Drafted → Sent (only after explicit user confirmation) → Done (row
  removed) / Blocked (blocker captured as a WO/ISS). A prompt row cannot
  close until every sub-question in it is answered and every suggestion or
  observation in the response is registered. Tracker must be empty before
  session close — an outstanding row is either unfinished work (carry
  forward as a WO) or an unregistered suggestion (a capture gap to fix
  before closing).
- **session-close-procedure**: before dispatching a close batch — sweep
  every open WO/ISS status against reality, capture any informal todo as a
  registry entry, and update the project's state files. Do not skip the
  survey step because "state is known"; do not draft the startup/handoff
  document directly without going through the close sweep; do not combine
  registry registration and the startup document into one commit — keep
  them separate so each is independently auditable. Project-specific close
  steps stay in that project's own rules, not here.

### CI

- **skip-ci-authoring-discipline / skip-ci-scope**: the skip-ci directive
  is evaluated against the push (commonly at HEAD, depending on the CI
  system), not scoped to the individual commit it's written on — tagging a
  meta commit with it can suppress CI for build-relevant work stacked
  underneath if that work hasn't already been pushed and built. Before
  tagging any commit, confirm every commit in the unpushed stack is
  docs/YAML-only (`git log origin/<default-branch>..HEAD --oneline`, checked against
  the actual file changes — not assumed). When in doubt, omit the directive
  and let CI run; a redundant build is cheap, a missed deploy is not. Never
  write the literal directive string in explanatory prose (a commit
  message describing a prior incident, a rule file, a runbook) — most CI
  systems substring-match it anywhere in the message, so a commit that
  merely *references* the directive can itself be skipped. Use an escaped
  form ("skip-ci", "the skip directive") in prose instead.

### Windows (applies only on Windows machines — ignore on Linux)

- **python-over-bash-on-windows**: for any script iterating over more than
  ~100 files, or performing more than ~1,000 subshell-equivalent operations
  (`$(...)` substitutions, piped commands in a loop, repeated `grep`/`find`
  calls), write it in Python, not bash. Git Bash on Windows pays ~50-100ms
  per forked subshell; loops that are fine on Linux CI regularly time out
  on a Windows dev box at scale. Bash stays fine for small linear glue
  (<100 iterations) and git hook scripts that must run under git's minimal
  environment.

### Registry vocabulary

- **status-vocabulary**: a status keyword and its date field are a fixed,
  paired vocabulary — don't invent synonyms. `complete`/`completed:` for
  delivered work, `resolved`/`resolved:` for a fixed issue,
  `closed`/`closed:` for an administrative closure without a fix,
  `superseded`/`superseded_by:`+`superseded:` when a later item replaces
  this one. `status: completed` and `status: done` are drift forms and are
  wrong — the status keyword has no `-ed` suffix; the suffix belongs on the
  date field only. Consistent vocabulary is what keeps a registry
  greppable; synonyms silently under-count every query and join against it.

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
