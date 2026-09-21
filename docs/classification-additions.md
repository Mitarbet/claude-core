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
