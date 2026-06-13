---
title: Agent guidelines
audience: agent
status: accepted
last_reviewed: 2026-06-13
owners: [maintainers]
---

# Agent guidelines (`.agents/`)

Modular, tool-agnostic guidelines for any coding agent — and any human — working in
Tapestry. The canonical entry point is the root [AGENTS.md](../AGENTS.md); it links here.

## Why a root `AGENTS.md` *and* this directory

`AGENTS.md` follows the open [agents.md](https://agents.md) standard (stewarded under the
Linux Foundation; read natively by Codex, GitHub Copilot, Cursor, Gemini CLI, Aider, Amp,
Jules, Junie, Zed, and more). The standard requires the file at the **repo root** for
discovery, so it can't move into this directory. Root `AGENTS.md` stays thin; the detailed,
**one-concern-per-file** guidelines live here.

## How each tool resolves to one source of truth

- **Most agents** read root `AGENTS.md` natively.
- **Claude Code** reads `CLAUDE.md`, which is a thin `@AGENTS.md` import (Windows-friendly — no symlink needed).
- **Tools needing scoped files** (Cursor `.cursor/rules/`, Copilot `.github/instructions/`, Cline `.clinerules/`) are **not set up yet**. When needed, **generate** them from this source (e.g. [`rulesync`](https://github.com/dyoshikawa/rulesync)) — never hand-maintain duplicates.

## Files

- [style-guide.md](style-guide.md) — documentation style (Markdown + HTML).
- [html-authoring-guide.md](html-authoring-guide.md) — the `/web` HTML standard.
- _(to come: code-style, testing, security, release — added when those areas open.)_
