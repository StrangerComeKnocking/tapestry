---
title: Documentation style guide
audience: agent
status: accepted
last_reviewed: 2026-06-13
owners: [maintainers]
---

# Documentation style guide

How to write documentation in Tapestry. Two audiences, two formats (see
[AGENTS.md](../AGENTS.md) §0): **agent-facing Markdown** (canonical) and
**human-facing HTML** (presentation). Read this before authoring anything.

## Principles (both formats)

- **One audience per document.** Write for an agent *or* a human; do not split the difference.
- **One [Diátaxis](https://diataxis.fr/) mode per document.** Tutorial, How-to, Reference, or Explanation — never a blend.
- **Single source of truth.** State a fact (a config key, an API shape, a default) exactly once, in `docs/reference/`. Everywhere else, link to it.
- **Answer first.** Lead with the conclusion or the command; put background after.
- **Own it and date it.** Every doc carries an `owner` and a `last_reviewed` date. Stale docs are bugs.

## Part A — Agent-facing Markdown (`/docs/**`)

Markdown is canonical. Optimize it for precise machine reading and clean diffs.

- **Front-matter.** Every file starts with YAML: `title`, `audience: agent`, `status`, `last_reviewed`, `owners`. ADRs/RFDs add their lifecycle fields.
- **Headings.** Exactly one `#` H1. Sentence case. ATX style (`##`, not underlines). Don't skip levels.
- **Semantic line breaks.** One sentence (or clause) per line. It reads the same when rendered but produces word-level diffs in review.
- **Structure for extraction.** Short paragraphs. Prefer tables and lists for anything structured. Use explicit **Do** / **Don't** blocks for rules.
- **Code.** Fenced blocks with a language tag. Commands must be copy-paste runnable, with exact versions/flags — no placeholders that "should work."
- **Links.** Relative within the repo. Stable, kebab-case heading anchors. No bare external URLs without an archived snapshot (see Part C).
- **No essential information in images.** A diagram may illustrate, but the text must stand alone for an agent that cannot see it.
- **Filenames.** kebab-case, one topic per file; `NNNN-` prefix for ADR/RFD.
- **Voice.** Imperative and terse. Define a term once; never market.

## Part B — Human-facing HTML (`/web/**`)

Hand-authored, crafted, accessible. The human presentation layer — it must never contradict the canonical Markdown.

> **Full rules:** see [html-authoring-guide.md](html-authoring-guide.md) (page skeleton, component vocabulary, design tokens, AI-first dual-surface). The below is the summary.

- **Semantic HTML5.** Real landmarks (`header`, `nav`, `main`, `article`, `footer`), headings in order, lists for lists, `figure`/`figcaption` for diagrams.
- **Accessibility: WCAG 2.2 AA.** Alt text on meaningful images, visible focus states, ≥4.5:1 text contrast, full keyboard navigation, `lang` set, labelled controls.
- **Resilient.** Content readable with **no JavaScript**; JS only enhances. Responsive from 320px up. Light + dark via `prefers-color-scheme`.
- **One design system.** A single design-token stylesheet in `web/assets/`. No per-page `<style>` blocks or inline styles for anything reusable.
- **Metadata.** `<title>`, meta description, canonical URL, Open Graph. Each page links to its Markdown source ("Read the spec").
- **Performance.** System or subset fonts, lazy-loaded images, minimal/zero third-party JS.
- **Diagrams.** Embed the **rendered** SVG (built from the Mermaid/D2 source in `docs/`) with a text alternative.

## Part C — Citations & longevity (both formats)

- **Archive every external citation at write-time.** Link the original URL **and** an archived snapshot ([Wayback "Save Page Now"](https://web.archive.org/) for routine sources, [Perma.cc](https://perma.cc/) for load-bearing ones).
- **Quote the load-bearing line inline**, so the claim survives even if both the source and its archive vanish.
- **Research notes** live in `docs/research/` and/or as the *Context* section of an ADR.

## Templates

- **ADR:** [MADR](https://adr.github.io/madr/) — `docs/decisions/NNNN-title.md`.
- **RFD:** Oxide-style — `docs/rfd/NNNN/README.md`.
- **Reference / How-to / Tutorial / Explanation:** start from the matching Diátaxis mode; a per-mode skeleton will live beside this guide.
