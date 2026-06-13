---
title: The documentation principle
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

# The documentation principle

> **Folders answer "what job am I doing?" Metadata answers everything else.**

This is the single rule the whole `docs/` system is built on. Before adding a
folder, a `kind`, or a rule, check it against this principle:

- If you're tempted to add a **folder** to capture an artifact type, an audience, a
  discipline, a lifecycle stage, or a sensitivity level — **stop.** Folders carry
  exactly one dimension (the reader's job). The rest is front-matter
  (see [`document-contract.md`](document-contract.md)).
- If you're tempted to add a **`kind`** that doesn't need distinct governance —
  it's a `tag`, not a kind (see [`kinds.md`](kinds.md)).
- Growth is allowed, but only through a record (ADR/RFD), never by duplication
  (see [`governance.md`](governance.md)).

Decided in [RFD-0001](../decide/rfd/0001-documentation-system.md).
