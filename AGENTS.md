# AGENTS.md — Tapestry

> Tapestry is an **AI-first, coding-agent-agnostic repository**. This file is the
> canonical, authoritative entry point for any agent — and any human — working here.
> **Read it first.** It follows the open [agents.md](https://agents.md) standard
> (now under the Linux Foundation): the **nearest** `AGENTS.md` to a file wins, so a
> subtree may override this one. Every coding agent resolves to this file (see §3).
>
> Scope today: **documentation structure + style** and **agent-instruction layout.**
> Other sections (build, test, code style, security, release) are stubs — see §7.
> Do not assume an unwritten rule; open an RFD (§5) instead.

---

## 0. The AI-first contract

1. **Agents are first-class contributors.** Docs are written for agents first, humans second — precise, terse, unambiguous, runnable.
2. **Markdown is the source of truth.** Anything an agent must act on lives in Markdown, in git, reviewed by PR.
3. **Two audiences, two formats:**

   | Audience | Format | Location | Status |
   |---|---|---|---|
   | **Agents** | **Markdown** | `/AGENTS.md`, `/CLAUDE.md`, `/llms.txt`, the guidelines in **`/.agents/`**, the knowledge base in **`/docs/`** | **Canonical** |
   | **Humans** | **HTML** (hand-crafted, accessible) | everything in **`/web/`** | **Presentation** |

4. **On conflict, `/docs` (Markdown) wins.** If `/web` HTML disagrees with `/docs`, the HTML is wrong — fix it. Canonical facts (config keys, API shapes, schemas) are stated **once** in `/docs/use/reference/` and never re-asserted elsewhere; other docs link to them.
5. **Determinism over vibes.** State exact versions, exact commands, explicit pre/postconditions. No "should just work."

---

## 1. Repository structure

```
tapestry/
├─ AGENTS.md                 # ← canonical, tool-agnostic agent instructions (root — required for discovery)
├─ CLAUDE.md                 # thin `@AGENTS.md` import (Claude Code reads CLAUDE.md, not AGENTS.md)
├─ llms.txt                  # AI-discovery index (llmstxt.org) — added with the site
├─ README.md                 # short; humans → /web, agents → AGENTS.md
│
├─ .agents/                  # AGENT GUIDELINES · Markdown · modular (one concern per file)
│   ├─ README.md             # index + how each tool resolves to AGENTS.md
│   ├─ style-guide.md        # documentation style (both formats)
│   └─ html-authoring-guide.md   # the /web HTML standard
│
├─ docs/                     # AGENT-FACING KNOWLEDGE BASE · Markdown · canonical (see RFD-0001 + docs/_meta/)
│   ├─ use/                  # use & self-host — Diátaxis + operate/, troubleshoot/, releases/
│   ├─ build/                # contribute to core — architecture/, contracts/, testing/
│   ├─ decide/               # immutable pipeline: research/ → rfd/ → decisions/ · specs/
│   ├─ direction/            # durable intent — vision, strategy, positioning, principles, roadmap
│   ├─ _generated/           # build output — do-not-edit
│   └─ _meta/                # document contract, kinds, governance, templates
│
└─ web/                      # HUMAN-FACING · hand-authored HTML + CSS
    ├─ index.html
    ├─ assets/               # design-token CSS, fonts, rendered diagram SVGs
    └─ …                     # the published human docs site
```

**`.agents/` = how to work** (guidelines/instructions). **`docs/` = what is true** (the knowledge base). Keep the two separate.

**Taxonomy:** the user docs in `docs/use/` follow [Diátaxis](https://diataxis.fr/) — *Tutorials, How-to, Reference, Explanation*, one mode per document. `decide/` is a lifecycle pipeline, not Diátaxis.

**Diagrams:** authored as **code** (Mermaid by default; D2 for high-fidelity art), beside the doc they illustrate (e.g. `docs/use/concepts/diagrams/`). Rendered SVG goes to `docs/_generated/` and `web/assets/` as *output*, never the source of truth.

---

## 2. Documentation style

The full rules live in **[.agents/style-guide.md](.agents/style-guide.md)** and, for the human site, **[.agents/html-authoring-guide.md](.agents/html-authoring-guide.md)**. Read them before authoring. The load-bearing rules:

- **Markdown (agent docs):** YAML front-matter on every file (`title`, `audience`, `status`, `last_reviewed`, `owners`); one `#` H1; sentence-case headings; one sentence per line (semantic line breaks → clean diffs); answer-first; tables/lists over prose; fenced code with a language tag; relative links; kebab-case filenames.
- **HTML (human docs):** semantic HTML5; WCAG 2.2 AA; responsive + dark mode; works without JS; one shared design-token stylesheet; a Markdown twin + `llms.txt` per the dual-surface rule.
- **Shared:** every doc has an owner and a `last_reviewed` date; external citations are archived (Wayback/Perma.cc) with an inline excerpt.

---

## 3. Agent instruction files (tool-agnostic)

Tapestry is **coding-agent agnostic**: one source of truth, and every tool resolves to it. Do not fork instructions per tool.

- **`AGENTS.md` (this file, repo root) is canonical.** It follows the open [agents.md](https://agents.md) standard and is read natively by Codex, GitHub Copilot, Cursor, Gemini CLI, Aider, Amp, Jules, Junie, Zed, and others. The standard requires it at the **root** for discovery, so it stays here — thin — and links to the detail.
- **Detailed guidelines live in [`.agents/`](.agents/)** — modular, one concern per file (style, HTML authoring, development, and later language-specific code-style and security). Edit guidance there or here; never inside a tool-specific file.
- **Claude Code** reads `CLAUDE.md`, not `AGENTS.md`. Our [`CLAUDE.md`](CLAUDE.md) is a one-line `@AGENTS.md` import, so Claude gets the canonical content. Claude-only, path-scoped rules may go in `.claude/rules/` (loaded on demand).
- **Tools needing their own scoped files** (Cursor `.cursor/rules/`, Copilot `.github/instructions/`, Cline `.clinerules/`) are **not set up yet**. When a contributor needs one, **generate** it from this source (e.g. [`rulesync`](https://github.com/dyoshikawa/rulesync)) rather than hand-maintaining a duplicate.
- **Nesting:** a subtree may add its own `AGENTS.md`; the nearest one to the edited file wins.

---

## 4. How agents work in this repo

- **Before acting:** read this file, then `/llms.txt` (when present), then the nearest subtree `AGENTS.md`.
- **Editing docs:** edit Markdown in `/docs`. Only touch `/web` HTML for explicit human-docs tasks, following [.agents/html-authoring-guide.md](.agents/html-authoring-guide.md). Never change meaning in `/web` without updating the canonical `/docs` source.
- **Writing code:** follow [.agents/development.md](.agents/development.md) — the loop (explore → plan → implement → verify → commit), verify-before-done, tests-as-spec, small self-contained changes, and the red lines.
- **Decisions:** record them. A small, mostly-irreversible choice → an **ADR** in `docs/decide/decisions/`. A substantial or externally-visible design → an **RFD** (§5). If an ADR starts needing a *Motivation* and *Alternatives* section, promote it to an RFD.
- **Never** delete a superseded ADR/RFD — mark it superseded and back-link.

---

## 5. Decisions & proposals (pointers)

- **ADRs** — [MADR](https://adr.github.io/madr/) format, `docs/decide/decisions/NNNN-*.md`, lifecycle `proposed → accepted → superseded`, linked to the implementing PR.
- **RFDs** — Oxide-style `docs/decide/rfd/NNNN/`, lifecycle `prediscussion → discussion → published → committed` (or `abandoned`); user-facing features additionally carry KEP-style `alpha → beta → stable` stage gates.

---

## 6. Conventions

- **Filenames:** kebab-case; `NNNN-` zero-padded prefix for ADR/RFD; one topic per file.
- **Links:** relative within the repo; never bare external URLs in canonical docs without an archived snapshot.
- **Commits/PRs:** Conventional Commits + DCO sign-off (enforced later in CI).

---

## 7. Not yet written (do not assume)

These govern "everything" eventually, but are intentionally empty until we get there. If you need one, **open an RFD** rather than inventing a rule:

- Build & dependency management
- Test & validation strategy
- Code style & language conventions
- Security & supply-chain
- Release & versioning

---

_Last reviewed: 2026-06-13 · Owner: maintainers_
