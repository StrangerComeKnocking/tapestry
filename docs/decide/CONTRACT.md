---
title: decide/ — area contract
kind: reference
area: decide
extends: ../_meta/document-contract.md
audience: [contributor, maintainer]
default_visibility: public
review_cadence: none
status: active
owner: "@maintainers"
last_reviewed: 2026-06-13
visibility: public
---

# `decide/` — area contract

**Job:** propose, evaluate, and record decisions — the immutable pipeline
`research/ → rfd/ → decisions/`, plus `specs/`.

**Belongs:** research notes, RFDs, ADRs, and specs (`kind: prd | pr-faq |
design-doc`, encoded as `NNNN-name.<kind>.md`).

**Does NOT belong:**
- how to *use* the result → `use/`
- the current-state truth → `build/`
- durable intent → `direction/`

**Rules:** only `kind: adr, status: accepted` is **truth**; an RFD never is.
Every spec declares its `kind` and links the RFD/ADR that authorized it.
