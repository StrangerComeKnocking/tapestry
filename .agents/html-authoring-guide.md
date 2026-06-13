---
title: HTML authoring guide
audience: agent
status: accepted
last_reviewed: 2026-06-13
owners: [maintainers]
---

# HTML authoring guide

How to author the human-facing HTML in `/web`. These rules are derived from how
front-running AI projects build their docs (Anthropic, Stripe, Cloudflare, Vercel,
Supabase, Hugging Face) and from web-platform best practice. They are binding:
author `/web` pages against this guide.

For the high-level format split and the Markdown rules, see [AGENTS.md](../AGENTS.md)
and [style-guide.md](style-guide.md). This guide is the detail for the HTML surface.

## 0. Principles

- **Dual-surface.** Every human page has a Markdown twin and is reachable as `.md`. The HTML is for people; the Markdown twin + `/llms.txt` are for agents. Author the content once; generate the twins (§13).
- **Semantic first.** The meaning lives in the HTML structure, not in CSS. A page must be fully readable with **CSS and JS disabled**.
- **Accessible by default.** WCAG 2.2 AA is the floor, not a stretch goal (§11).
- **Fast by default.** No layout shift, minimal JS, system/subset fonts (§12).
- **A small, fixed vocabulary.** Use the components in §4 and nothing ad hoc. Consistency is the product.
- **Never contradict `/docs`.** Canonical facts live once in `/docs/reference`; pages link to them.

## 1. Page architecture

- **Three-column shell** (the universal AI-docs layout): left **nav sidebar**, center **content**, right **on-this-page TOC**; **breadcrumbs** above the `<h1>`. Reserve full-width for landing/overview pages only.
- **Diátaxis axis** is primary: hand-authored **guides** (tutorials, how-to, explanation) are visually and structurally distinct from generated **reference**.
- **Overview pages are launchpads, not prose** — a grid of cards + a "recommended path", not paragraphs.

## 2. Page skeleton (canonical boilerplate)

Every page starts from this. It encodes landmarks, the skip link, metadata, the dual-surface link, and structured data.

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Page title · Tapestry</title>
  <meta name="description" content="One-sentence summary.">
  <link rel="canonical" href="https://…/page/">
  <link rel="alternate" type="text/markdown" href="./page.md">   <!-- the Markdown twin -->
  <meta property="og:title" content="Page title · Tapestry">
  <meta property="og:type" content="article">
  <link rel="stylesheet" href="/assets/css/tokens.css">
  <link rel="stylesheet" href="/assets/css/base.css">
  <script type="application/ld+json">
  { "@context":"https://schema.org", "@type":"TechArticle",
    "headline":"Page title", "dateModified":"2026-06-13" }
  </script>
</head>
<body>
  <a class="skip-link" href="#main">Skip to content</a>
  <header role="banner"><!-- brand, search (⌘K), theme toggle --></header>
  <div class="layout">
    <nav aria-label="Docs"><!-- sidebar --></nav>
    <main id="main">
      <nav aria-label="Breadcrumb"><!-- … --></nav>
      <article>
        <h1>Page title</h1>
        <!-- content -->
      </article>
    </main>
    <aside aria-label="On this page"><!-- TOC, scroll-spy --></aside>
  </div>
  <footer role="contentinfo"><!-- links to Markdown source, repo --></footer>
</body>
</html>
```

- Exactly one `<h1>` and one `<main id="main">`. Headings in order, never skipped.
- Every heading gets a stable, slugged `id` so agents can deep-link and cite (`<h2 id="page-skeleton">`).

## 3. Component vocabulary (the only allowed set)

Author these as semantic HTML; do not invent variants.

1. **Callout** — `<aside class="callout callout--warning">` with an **icon + visible label** ("Warning"), so meaning survives grayscale. Variants: note, tip, warning, danger.
2. **Steps** — an ordered procedure: `<ol class="steps">`.
3. **Tabs** — e.g. by language/runtime. Real buttons with `role="tab"`/`aria-selected`; content in `role="tabpanel"`. Content of all panels present in the DOM (agent-readable).
4. **Card / card grid** — `<a class="card">` inside `<ul class="card-grid">` for navigational overviews.
5. **Code block** — see §8.
6. **Accordion** — native `<details><summary>` (works without JS).
7. **Parameter table** — `<table>` with `<th scope="col">` columns name / type / required / description.
8. **Figure** — `<figure>` + `<figcaption>` for every diagram/illustration (§9).

## 4. Design tokens & CSS architecture

- **Two tiers of tokens** in `:root`: raw/base (`--blue-500`, never used directly) → semantic (`--color-text`, `--color-bg`, `--color-accent`, `--color-border`). Components reference only semantic tokens.
- Scales as variables: **type** (modular ~1.2–1.25), **space** (4/8px multiples, `--space-1`…), **radius**, **shadow**.
- **DON'T** hard-code hex/px in component CSS. **DON'T** use per-page `<style>` for anything reusable — one shared system in `/web/assets/css`.

```css
:root{
  --blue-600:#2563eb;                       /* base */
  --color-bg:#fff; --color-text:#1f2937;    /* semantic (light) */
  --color-accent:var(--blue-600);
  --space-1:.25rem; --space-2:.5rem; --space-4:1rem;
  --radius-md:.5rem;
}
@media (prefers-color-scheme:dark){
  :root{ --color-bg:#0b0f17; --color-text:#e5e7eb; }
}
:root[data-theme="dark"]{ --color-bg:#0b0f17; --color-text:#e5e7eb; } /* manual toggle */
```

## 5. Typography

- **DO** constrain prose to `max-width: 65ch` (sweet spot 50–75 chars/line). Body `line-height: 1.5–1.65`; headings ~1.2.
- **DO** use fluid type with a `rem` term so zoom still works: `font-size: clamp(1rem, .95rem + .4vw, 1.125rem)`.
- **DON'T** size text with `vw` alone (breaks zoom → fails WCAG 1.4.4).

## 6. Color & dark mode

- Ship **semantic tokens that flip** under `@media (prefers-color-scheme: dark)`, **plus** a `[data-theme]` manual override; the toggle reads `localStorage` first, else system (three-way).
- Meet WCAG 2.2 **1.4.3** (4.5:1 body, 3:1 large/bold) and **1.4.11** (3:1 UI components/graphics). **Verify both themes** — a token that passes in light may fail in dark.
- **Never** convey state by color alone.

## 7. Code blocks

- **Highlight at build time** (Shiki — inline styles, zero client JS). Never ship a runtime highlighter that blocks paint.
- Header with **filename/language**; a **copy button** (`navigator.clipboard`, `aria-label="Copy code"`); make the `<pre>` focusable (`tabindex="0"`) so keyboard users can scroll it.
- **Horizontal scroll**, don't wrap (wrapping breaks indentation). Inline `<code>`: subtle bg + padding, no syntax colors.

## 8. Callouts, tables, figures

- Wrap every diagram/illustration in `<figure>` + `<figcaption>`; embed the **rendered SVG** built from the Mermaid/D2 source in `docs/explanation/diagrams/` (source of truth stays in `/docs`). Informative images need real `alt`; decorative get `alt=""`.
- Real `<table>` with `<th scope>`; wrap wide tables in a focusable scroll container.

## 9. Navigation

- Sticky sidebar with a **visible active state**; on-this-page TOC with **scroll-spy**; a **skip-to-content** link that is off-screen until `:focus` (never `display:none` — that drops it from tab order).
- Command-**⌘K** search is expected; keyboard-navigable; mobile drawer for the sidebar.

## 10. Accessibility — WCAG 2.2 AA (checklist)

- Landmarks (`header`/`nav`/`main`/`aside`/`footer`); correct heading order.
- `:focus-visible` outlines ≥2px, 3:1 contrast against both states.
- Honor `prefers-reduced-motion: reduce` (drop transform/scale animation).
- **Target size ≥ 24×24 CSS px** (2.5.8), except inline links.
- **No ARIA where native HTML suffices** — "no ARIA is better than bad ARIA."

## 11. Performance (checklist)

- `system-ui` stack or **one** subset variable WOFF2 (`unicode-range`), self-hosted, `font-display: swap`/`optional`, `size-adjust` to kill font-swap CLS.
- Explicit `width`/`height` or `aspect-ratio` on media; `loading="lazy"` below the fold.
- Minimal JS, **progressively enhanced** (readable with JS off). **Inline SVG** for icons — never icon fonts.

## 12. AI-first affordances (the dual surface)

This is non-negotiable for an AI-first repo. The HTML must also be agent-consumable.

- **`/llms.txt`** at site root: `# Tapestry` H1, a one-line `>` blockquote summary, then `##` sections of `[name](url): notes` links pointing at the **`.md`** twins; an `## Optional` section for droppable links. Add **`/llms-full.txt`** (all page Markdown concatenated). Generate both at build from front-matter/sitemap.
- **Per-page Markdown:** every page reachable by appending **`.md`**, and via **content negotiation** (`Accept: text/markdown` → Markdown, else HTML). On Cloudflare Pages, do the negotiation in a Worker; advertise discovery with a `Link: </llms.txt>; rel="llms-txt"` header.
- **"Copy page" / "Open in Claude/ChatGPT"** control in the page header, sourced from the `.md` twin.
- **Machine-readable HTML:** server-rendered content (never JS-gated), stable heading `id`s, **JSON-LD `schema.org/TechArticle`**, `sitemap.xml` (list pages and their `.md` twins), and a `robots.txt` that states the policy for `GPTBot`/`ClaudeBot`/`PerplexityBot`.
- **CI gate:** fail the build if any HTML page lacks its `.md` twin, a sitemap entry, a `<title>`, a meta description, or an `<h1>`.

## 13. Pre-publish checklist

- [ ] Renders fully with **CSS off** and **JS off**.
- [ ] One `<h1>`, ordered headings, all headings have `id`s.
- [ ] Skip link, landmarks, `:focus-visible`, reduced-motion honored.
- [ ] Contrast passes in **both** light and dark.
- [ ] Code blocks: copy button + keyboard-scrollable.
- [ ] `.md` twin exists; `llms.txt`/sitemap updated; JSON-LD present.
- [ ] No CLS; images sized; fonts subset; JS minimal.
- [ ] Links to its Markdown source; canonical facts link to `/docs/reference`.

## References

- Diátaxis — https://diataxis.fr/
- WCAG 2.2 (target size, contrast) — https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html · https://www.w3.org/WAI/WCAG21/Understanding/contrast-minimum.html
- GitHub Primer tokens — https://primer.style/foundations/color/overview
- Font best practices (CLS) — https://web.dev/articles/font-best-practices
- Skip nav — https://webaim.org/techniques/skipnav/
- Shiki syntax highlighting — https://docs.astro.build/en/guides/syntax-highlighting/
- llms.txt spec — https://llmstxt.org/ · Anthropic live file — https://platform.claude.com/docs/llms.txt
- Mintlify Markdown export / content negotiation — https://www.mintlify.com/docs/ai/markdown-export · https://www.mintlify.com/blog/context-for-agents
- schema.org/TechArticle — https://schema.org/TechArticle
