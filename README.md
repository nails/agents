# Nails agent kit

Conventions and skills for agents working on the Nails framework.

These files apply when you are in the framework checkout: the directory `nails dev:pull` fills with every Nails repository. A single module cloned on its own does not receive them.

## How to use

1. Edit `AGENTS.md` (always-on conventions) or add a skill under `skills/` (a directory containing `SKILL.md`).
2. Run `nails dev:pull` from the framework checkout.

`dev:pull` clones this repository as `agents/` and symlinks at the checkout root:

- `AGENTS.md` → `agents/AGENTS.md`
- `CLAUDE.md` → `agents/CLAUDE.md`
- `.agents/skills`, `.claude/skills`, `.cursor/skills` → `agents/skills`

`CLAUDE.md` is one line, `@AGENTS.md`, so Claude Code reads the same conventions as Cursor, Codex, and Copilot.

Do not edit the symlinks. Change this repository, then pull again. If `agents/` is missing, `dev:pull` skips the links.

## What belongs where

- **Conventions** go in `AGENTS.md`. Short, always on.
- **Skills** go in `skills/<name>/SKILL.md`. Procedures the agent loads when the task matches.
