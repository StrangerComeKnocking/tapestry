---
id: ADR-0001
title: Record architecture decisions as ADRs
kind: adr
audience: [contributor, maintainer]
status: accepted
canonical: true
source: authored
owner: "@maintainers"
last_reviewed: 2026-06-13
review_cadence: none
visibility: public
related: [RFD-0001]
---

# ADR-0001 — Record architecture decisions as ADRs

## Status

Accepted.

## Context

We need a durable, low-friction record of architecturally-significant decisions, so
rationale survives contributor turnover and agents don't re-litigate settled choices.

## Decision

Record each significant decision as an ADR in `docs/decide/decisions/`, in the
[MADR](https://adr.github.io/madr/) format, numbered `NNNN-kebab-title.md`, immutable
once accepted. A reversed decision is kept and marked `superseded_by`. Substantial or
cross-cutting proposals use an RFD instead (see
[RFD-0001](../rfd/0001-documentation-system.md)); an accepted RFD may spawn ADRs.

## Consequences

- Decisions are versioned, reviewed, and citeable by `id`.
- Only `status: accepted` ADRs are "truth"; proposals (RFDs) are not.
- Superseded ADRs remain for the historical record.
