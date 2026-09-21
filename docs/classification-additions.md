# Additions to the rule classification

The compliance-platform classification lives in Windows Claude memory
(`C:\Users\paulh\.claude\projects\C--DevEnv-claude-core\memory\`), which is not
in this repo. These items were added on the Linux side on 2026-09-21 and must be
folded into that classification when the real `BASELINE.md` is written.

## New universal role
- **Orchestrator** — coordinates, does not code or push. Reads state, confirms
  drift against git ground truth, assigns WOs to functional terminals, approves
  or gates what Prime pushes, runs session-close, numbers and logs prompts.
  Full definition: `BASELINE.md`, section "Orchestrator".

## New universal rules
- **git-ground-truth** — at session start run git log, git status and
  git rev-parse HEAD; trust them over state files and prior summaries.
- **confirm-before-outward-actions** — confirm with the user before anything
  hard to reverse or outward-facing (push, deploy, delete, cancel, publish).

## Verified
The user-level loader (`~/.claude/CLAUDE.md` with an `@` import of
`BASELINE.md`) was tested on 2026-09-21: a fresh headless session in an empty
folder loaded all rules and both roles from the baseline.

## Decision: loader location (2026-09-21)
The loader is a **user-level** CLAUDE.md on each machine, not a parent-directory
`C:\DevEnv\CLAUDE.md`. This supersedes the "Location" design decision in
`docs/memory/project_claude_core_baseline.md`.

Why: a parent-directory file only loads for projects under that directory. On
Linux the projects live in `~/AIProjects/`, not `~/DevEnv/`, so it would never
load there. A user-level file loads for every project regardless of location.

| Machine | Loader file | Contents |
|---|---|---|
| Linux | `~/.claude/CLAUDE.md` | created and tested 2026-09-21 |
| Windows | `%USERPROFILE%\.claude\CLAUDE.md` | NOT yet created; create after the real `BASELINE.md` is written |

Windows loader text:
```
claude-core baseline: C:\DevEnv\claude-core\BASELINE.md
@C:\DevEnv\claude-core\BASELINE.md
```
When the real baseline is done, also remove the `C:\DevEnv\CLAUDE.md` step from
the "How to apply" in the memory file; it is no longer needed.
