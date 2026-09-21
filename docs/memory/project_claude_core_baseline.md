---
name: project-claude-core-baseline
description: Status and design decisions for the claude-core BASELINE.md project — shared universal rules extracted from compliance-platform and manuscripts-ai
metadata: 
  node_type: memory
  type: project
  originSessionId: 2dc634a3-9401-43ab-87dd-1b86a5c9371d
---

Classification and design work is complete but WRITING IS ON HOLD pending testing of other context-reduction changes already made to compliance-platform.

**Why:** compliance-platform was loading ~180K tokens per session (~99 rule files). Other changes were made first; the BASELINE migration will only proceed once those changes are tested and the net token savings are measured.

**Goal:** Reduce compliance-platform from ~180K to ~80–100K tokens by extracting universal rules into `C:\DevEnv\claude-core\BASELINE.md`.

## Design decisions (confirmed, awaiting implementation)

**Location:** `C:\DevEnv\CLAUDE.md` (3–5 lines) points to `C:\DevEnv\claude-core\BASELINE.md`. Parent CLAUDE.md auto-loads for all projects under `C:\DevEnv\` — no per-project stub needed. manuscripts-ai already has a "claude-core under construction" stub that will become redundant once BASELINE exists.

**BASELINE content:** Stripped-down versions of universal rules (remove compliance-specific Sprint war stories / examples) targeting ~1,500 lines.

## 18 confirmed UNIVERSAL rules (move to BASELINE)

Core git safety:
- no-parallel-commits.md (566 lines — highest impact)
- git-identity-per-terminal.md
- non-prime-fetch-on-prompt.md
- prime-push-all-unpushed.md
- pre-push-authorization-fetch.md
- pre-rebase-discipline.md
- session-open-inherited-mods.md

WO lifecycle:
- wo-envelope-commit.md
- wo-completion-criteria.md
- no-unlogged-suggestions.md
- brief-inventory-reconciliation.md

Orchestration:
- prompt-tracking-discipline.md (488 lines)
- session-close-procedure.md (strip Steps 2.6 and compliance PMO format)
- sprint-close-sweep.md

Shared-file safety:
- yaml-edit-reread.md

Windows:
- python-over-bash-on-windows.md

CI:
- skip-ci-authoring-discipline.md
- skip-ci-scope.md

## Rules that stay in compliance-platform
All AWS/ECS/IAM/RDS, Alembic/PostgreSQL, compliance product logic, Docker, repomix, codex-dispatch, orchestrator-self-assessment, terminal-roles (too compliance-terminal-specific), and all naming/state-file rules that reference compliance-specific YAML paths.

## Three borderline rules — include concept only, not verbatim
- terminal-roles.md → replace with a 10-line "terminal role framework" section in BASELINE
- session-close-procedure.md → include stripped (remove psql DB count queries)
- status-vocabulary.md → include the vocabulary table only, strip compliance YAML path references

**How to apply:** When user is ready to proceed, write BASELINE.md using stripped content from the 18 rule files listed above, then update C:\DevEnv\CLAUDE.md and remove the corresponding files from compliance-platform's .claude/rules/ directory.
