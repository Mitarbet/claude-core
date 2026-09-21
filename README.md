# claude-core

Shared Claude Code baseline for all projects: universal rules, the Prime and
Orchestrator roles, and (later) stack layers.

## Layout
- `BASELINE.md` — universal rules and roles. Loaded in every project.
- `stacks/` — per-stack rule layers (not yet written).

## Locations
| Machine | Path |
|---|---|
| Windows | `C:\DevEnv\claude-core` |
| Linux | `~/DevEnv/claude-core` |

Each machine's user-level Claude file loads `BASELINE.md` via the same
`@~/DevEnv/claude-core/BASELINE.md` import: Windows `%USERPROFILE%\.claude\CLAUDE.md`,
Linux `~/.claude/CLAUDE.md`. On Windows this needs a junction
(`mklink /J %USERPROFILE%\DevEnv C:\DevEnv`); absolute `C:\` paths do not resolve
in `@` imports. See `docs/classification-additions.md`.

## Working on two machines
Git is the sync. Run `git pull` at the start of every session and `git push`
after every change. Do not put this folder on OneDrive, Dropbox or a share.
Never commit secrets here.
