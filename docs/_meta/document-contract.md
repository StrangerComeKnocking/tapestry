---
title: The document contract
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

# The document contract

Every Markdown doc under `docs/` carries this front-matter. CI validates it; a
missing or invalid field fails the build. **The front-matter is the information
architecture** — retrieval, freshness, ownership, and classification all read from
it, not from the folder path.

## Required fields

| Field | Type | Notes |
|---|---|---|
| `title` | string | — |
| `kind` | enum | from [`kinds.md`](kinds.md) — closed, flat |
| `audience` | list | `user · operator · contributor · maintainer` |
| `status` | enum | per-kind lifecycle (below) |
| `source` | enum | `authored · generated · summary` |
| `owner` | string | a CODEOWNERS handle |
| `last_reviewed` | date | — |
| `review_cadence` | duration \| `none` | `none` only for immutable records |
| `visibility` | enum | `public · internal · restricted` (only `public` in this repo) |

## Optional fields

| Field | Notes |
|---|---|
| `id` | stable, unique, citeable (required for records: `ADR-NNNN`, `RFD-NNNN`) |
| `canonical` | `true` ⇒ source of truth for its topic (CI: unique per topic) |
| `applies_to` | version map, e.g. `{ product: ">=0.3", api: v1 }` |
| `verified_against` | the version a how-to/runbook was last verified on |
| `supersedes` / `superseded_by` | record links; setting `superseded_by` ⇒ `status: superseded` |
| `related` | list of ids |
| `tag` | free-form sub-classification *within* a `kind` (NOT a new kind) |

## Lifecycle (`status`) by kind family

- **records** (`adr`): `proposed → accepted → superseded \| deprecated`
- **records** (`rfd`): `draft → discussion → published → committed \| abandoned`
- **living** (everything else): `draft → active → deprecated → superseded`
- Only `kind: adr, status: accepted` may be cited as **truth**. An `rfd` never is.

## Area contracts

Each top-level area carries a delta-only `CONTRACT.md`. It is structured
front-matter — `area`, `extends: _meta/document-contract.md`, `purpose`,
`audience`, `default_visibility`, optional `review_cadence` override — plus a short
**belongs / does-not-belong** list. It **must not restate** any rule from this
file (see [`governance.md`](governance.md)).
