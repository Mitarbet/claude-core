# claude-core — Session Resume

## Load memory before proceeding

Read these files in order:

1. `C:\Users\paulh\.claude\projects\C--DevEnv-claude-core\memory\MEMORY.md`
2. `C:\Users\paulh\.claude\projects\C--DevEnv-claude-core\memory\project_claude_core_baseline.md`

## Context

This project is a shared baseline for Claude Code discipline rules currently
duplicated across projects under `C:\DevEnv\`.

**Problem:** `C:\DevEnv\compliance-platform` loads ~99 rule files (~180K tokens
per session). Many rules are universal (git discipline, multi-terminal
coordination, WO lifecycle, session procedures) and have nothing to do with
compliance software. They are also duplicated inline in
`C:\DevEnv\manuscripts-ai\CLAUDE.md`.

**Goal:** Create `C:\DevEnv\claude-core\BASELINE.md` containing only universal
rules, so each project CLAUDE.md stays lean and project-specific.
Target: reduce compliance-platform from ~180K to ~80–100K tokens per turn.

## Current status

Classification and design work is **complete**. Writing is **on hold** pending
testing of other context-reduction changes already made to compliance-platform.

All decisions are in memory. When ready to proceed, say:

> "Proceed with writing BASELINE.md — classification is in memory."

## Note added 2026-09-21 (Linux session): provisional BASELINE.md exists

A **provisional** `BASELINE.md` is already in this repo. It was written on the
Linux server from manuscripts-ai's five inline rules only (prompt-tracking,
no-parallel-commits, wo-envelope-commit, session-close, brief-inventory), plus
two additions (git-ground-truth, confirm-before-outward-actions) and the
**Prime** and **Orchestrator** role definitions. It is about 2.6KB and does NOT
reflect the compliance-platform classification in memory.

**When proceeding with "Proceed with writing BASELINE.md":**
- **Merge, do not overwrite.** The classification in memory is the authority for
  which rules are universal. Fold it into the existing `BASELINE.md`.
- **Keep the Orchestrator role.** It is a new universal role (reads state,
  confirms drift against git ground truth, assigns WOs, gates Prime's pushes,
  runs session-close) that is not in the classification. Add it to the design.
- Keep the two added rules unless the user removes them.
- The Windows user-level loader (`%USERPROFILE%\.claude\CLAUDE.md`) has NOT been
  created yet. Create it only after the real baseline is written, so
  compliance-platform does not load the provisional file.

## Two-machine setup
- Windows: `C:\DevEnv\claude-core`. Linux: `~/DevEnv/claude-core`.
- Git is the sync: `git pull` at session start, `git push` after changes.
- The memory files above live only on Windows and are not in this repo.
  Copy their contents into the repo (for example `docs/`) if Linux sessions
  need them.
