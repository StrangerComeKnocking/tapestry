# CLAUDE.md

@AGENTS.md

> Claude Code reads `CLAUDE.md`, **not** `AGENTS.md`, so this file imports the
> canonical, tool-agnostic instructions above via `@AGENTS.md`. The single source
> of truth is `AGENTS.md` + the guidelines in [`.agents/`](.agents/). Put
> Claude-specific, path-scoped rules in `.claude/rules/` (loaded on demand).
> **Do not duplicate guidance here** — edit `AGENTS.md` or the files in `.agents/`.
