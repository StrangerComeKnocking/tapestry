---
title: Documentation governance
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

# Documentation governance

The rules that keep the system from drifting back into soup. All enforced by
`docs-ci.yml` (sequenced per [RFD-0001](../decide/rfd/0001-documentation-system.md)).
**The meta-rule: growth is allowed only through a record (ADR/RFD), never by
duplication.**

## 1. The spine is fixed

```
docs/
├─ use/         # use & self-host Tapestry        (user / operator)
├─ build/       # contribute to Tapestry core      (contributor)
├─ decide/      # research → rfd → decisions · specs (immutable pipeline)
├─ direction/   # durable intent                    (vision/strategy/positioning/principles/bets/roadmap-pointer)
├─ _generated/  # build output — do-not-edit
└─ _meta/       # these rules
```

New top-level folders are **not** added for new disciplines (security, ops,
design, product, data, community). Those are handled by `kind` + `visibility`. A
new root folder requires an **RFD** with overwhelming evidence it deserves
first-class navigation.

## 2. `_meta/` is allowlisted

`_meta/` may contain only: `PRINCIPLE.md`, `document-contract.md`, `kinds.md`,
`governance.md`, `glossary.md`, and `templates/` (exactly one template per `kind`).
Anything else fails CI. Adding to the allowlist requires an RFD.

## 3. `kind` is a closed, flat enum

See [`kinds.md`](kinds.md). CI rejects an unknown `kind` or any `kind` containing
`/`. A new kind requires an ADR and must justify *distinct governance*; otherwise
it is a `tag`.

## 4. Area contracts are delta-only

An area `CONTRACT.md` carries only its delta (purpose, audience, default
visibility, cadence override, belongs / does-not-belong) and `extends` the global
[`document-contract.md`](document-contract.md). It **must not restate** a global
rule. CI fails an area contract that duplicates global prose. (Nothing to copy =
nothing to drift.)

## 5. Single-source invariant for instructions

- `CLAUDE.md` = `@AGENTS.md` and nothing else (CI fails on any other content).
- `AGENTS.md` **links** the files in `.agents/`; it never restates them.
- Every rule lives in exactly **one** `.agents/` file. CI lints for a
  rule/heading appearing in two places.

## 6. Sensitivity gate

This is a public repo. CI **fails** any doc with `visibility != public`, plus
secret-scanning. `internal`/`restricted` content lives only in
`tapestry-internal`; only sanitized outputs (published postmortems, `SECURITY.md`)
cross into the public repo.

## 7. Freshness & canonicality

- CI flags any doc past `last_reviewed + review_cadence` and pings its `owner`.
  `operate/` and `troubleshoot/` use a short cadence (30–90d).
- Exactly one doc per topic is `canonical: true`; CI enforces uniqueness.
  `source: generated` docs live only in `_generated/` and are never hand-edited.
