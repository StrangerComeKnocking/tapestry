---
id: RFD-0001
title: Documentation system
kind: rfd
audience: [contributor, maintainer]
status: committed          # discussion → published → committed (= accepted & in effect)
canonical: true
source: authored
owner: "@maintainers"
last_reviewed: 2026-06-13
review_cadence: none       # records are immutable once committed
visibility: public
related: [ADR-0001]
---

# RFD-0001 — Documentation system

> **Status: committed.** This records the decision; the *living* rules live in
> [`docs/_meta/`](../../_meta/) and are linked, not restated, below.

## Summary

Tapestry's documentation is organized by **one principle**:

> **Folders answer "what job am I doing?" Metadata answers everything else.**

A small, job-based folder spine (`use/ build/ decide/ direction/`) carries the
single dimension of *reader intent*. Every other dimension — artifact kind,
lifecycle, ownership, freshness, canonicality, version, **sensitivity** — is
carried by front-matter (the document contract) and enforced by CI, not by adding
folders.

## Context

An earlier draft organized `docs/` by a mix of audiences, org functions, artifact
types, and lifecycle stages (`tutorials/ product/ architecture/ operations/
security/ design/ decisions/ rfd/ research/ community/`). Review found this is
"category soup": a folder hierarchy is one-dimensional, but documentation has
**five orthogonal dimensions** (audience × lifecycle × discipline × sensitivity ×
canonicality). Forcing five dimensions into one tree *is* the soup, and it fails
silently at scale — drift, leakage, and duplicate truth — while still *looking*
organized.

## Decision

Four dimensions, four mechanisms:

| Dimension | Mechanism |
|---|---|
| Sensitivity (public / internal / restricted) | **which repo** — public `tapestry`, private `tapestry-internal` |
| Reader's job | **the folder** (one axis): `use/ build/ decide/ direction/` |
| Kind · status · canonical · freshness · version | **front-matter** — see [`_meta/document-contract.md`](../../_meta/document-contract.md) |
| Keeping it honest | **CI** (`docs-ci.yml`) — sequenced in Rollout |

- **Spine & per-area contracts:** `use/` (use & self-host), `build/` (contribute to
  core), `decide/` (the immutable research → rfd → decisions pipeline + specs),
  `direction/` (durable intent). Each area has a delta-only `CONTRACT.md`.
- **Document contract:** required front-matter on every doc — see
  [`_meta/document-contract.md`](../../_meta/document-contract.md).
- **Kinds:** a closed, flat vocabulary — see [`_meta/kinds.md`](../../_meta/kinds.md).
  `kind` is the real category; it is metadata, never a folder.
- **Generated vs source:** machine contracts live in `build/contracts/`; their
  human reference is `source: generated` into `_generated/`. One source.
- **Sensitivity:** the public repo is public-only; risk domains (`run/`,
  `security/`) live in `tapestry-internal`. Only sanitized outputs cross over.
- **Anti-sprawl governance:** see [`_meta/governance.md`](../../_meta/governance.md).

The full tree is reproduced in `_meta/governance.md`.

## Alternatives considered

- **By-discipline** (GitLab-style `product/ engineering/ …`): clear ownership but
  buries the user journey and scatters how-tos. Rejected.
- **By-Diátaxis only** (`tutorials/ how-to/ reference/ explanation/`): no home for
  ops, decisions, or specs. Rejected.
- **By-lifecycle** (`define/ decide/ build/ run/`): readers think in jobs, not
  stages. Rejected as the *primary* axis (lifecycle is carried by `status`).
- **Minimum spine, defer the rest** (`use/ build/ decide/` only): considered;
  rejected in favor of committing the whole *design* now while *sequencing* the CI.

## Drawbacks & mitigations

Every governance surface can itself sprawl. Mitigations (all in
[`_meta/governance.md`](../../_meta/governance.md)):

| Risk | Mitigation |
|---|---|
| `_meta/` becomes a junk drawer | `_meta/` is an **allowlisted** fixed set; additions need an RFD |
| `kind` becomes shadow folders | `kind` is a **closed, flat enum**; a kind needs distinct governance or it's a `tag`; add/remove via ADR |
| area `CONTRACT.md` drifts | area contracts are **delta-only front-matter** that `extends` the global; never restate global rules |
| `AGENTS.md`/`CLAUDE.md`/`.agents` duplicate | **single-source invariant**: `CLAUDE.md` = `@AGENTS.md` only; `AGENTS.md` links `.agents/*`, never restates |

The through-line: **growth is allowed only through a record (ADR/RFD), never by duplication.**

## Rollout

The *design* is whole; *enforcement* is sequenced:

1. **This commit** — the spine, the document contract, `kinds.md`, `governance.md`, per-area contracts, ADR-0001.
2. **Next** — `.github/CODEOWNERS` + `docs-ci.yml` checks: required front-matter, enum validation, canonical-uniqueness, staleness, the public-only visibility gate, link-check.
3. **At first generated doc / pre-public-launch** — the `_generated/` pipeline (API/CLI/schema reference, `index.json`, `llms.txt`).
