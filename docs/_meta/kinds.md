---
title: Kind vocabulary
kind: reference
audience: [contributor, maintainer]
status: active
canonical: true
source: authored
owner: "@maintainers"
last_reviewed: 2026-06-13
review_cadence: 365d
visibility: public
---

# Kind vocabulary

`kind` is the real category for a document — carried as metadata, never as a
folder. This list is **closed and flat** (no `/`, no nesting). It is the single
source of the enum CI validates against.

**The test for a new kind:** a `kind` exists only if it needs *distinct
governance* — different required fields, lifecycle, owner, or cadence. If two
candidates would share the same governance, they are **one kind + a `tag`**, not
two kinds. Adding or removing a kind requires an **ADR**.

## The enum

| Area | Kinds |
|---|---|
| `use/` | `tutorial` · `how-to` · `reference` · `concept` · `troubleshooting` · `release-note` · `migration-guide` · `metric-def` |
| `build/` | `architecture` · `contract` · `test-strategy` |
| `decide/` | `research` · `rfd` · `adr` · `prd` · `pr-faq` · `design-doc` |
| `direction/` | `direction` · `roadmap` |
| *private repo* | `runbook` · `slo` · `incident` · `threat-model` · `audit` |

## Notes on consolidation (the rule in action)

- **`direction`** is one kind for vision, strategy, positioning, principles, and
  strategic bets — they share governance (durable, maintainer-owned, slow cadence).
  Distinguish them with `tag: [vision|strategy|positioning|principle|bet]`, not new
  kinds. `roadmap` is separate because it has different governance (a pointer:
  `source: summary`, must link the live planning source).
- **`contract`** covers `api` / `schema` / `proto` via `tag`, not separate kinds.
- **specs** in `decide/specs/` must declare `kind: prd | pr-faq | design-doc`
  (encoded in the filename too: `NNNN-name.<kind>.md`). There is no generic `spec`
  kind — that ambiguity is the junk drawer we're avoiding.

Soft cap: ~20 kinds. If the list is growing fast, the growth is probably `tag`s
wearing kind costumes.
