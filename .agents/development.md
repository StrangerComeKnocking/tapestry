---
title: Development guidelines
audience: agent
status: accepted
last_reviewed: 2026-06-13
owners: [maintainers]
---

# Development guidelines

How every change to Tapestry gets made — agents and humans alike. Reads with
[AGENTS.md](../AGENTS.md) and [RFD-0001](../docs/decide/rfd/0001-documentation-system.md);
details the *engineering workflow*, never restates them.

> Tapestry is a **database**. A silent bug means corrupted or lost data. The bar:
> **fail loudly, verify everything, change small.**

## 1 · The loop — every change

`explore → plan → implement → verify → commit`. A loop, not a straight line. Read the
relevant code and existing patterns **before** proposing a change — solve the real
problem, not a plausible one.

| The change is… | Do |
|---|---|
| describable in one sentence (typo, rename, log line) | just code it |
| multi-file, unfamiliar, or uncertain approach | plan first |
| cross-team, or touches a **public API / data model / storage format / wire protocol** | **open an RFD first** — these are one-way doors |

For a sizeable feature: interview the human → write a self-contained spec in
`docs/decide/specs/` → implement it in a fresh context.

## 2 · Verify before "done" — non-negotiable

"Done" = **shippable**, not "code written." Before you claim it:

- Run build, format, lint, type-check, tests — and **show the command + output**.
  Never assert success you didn't observe.
- Fix the **root cause**, never the symptom. Never silence a check to go green.
- Can't verify it (a test, a script, an output)? Then don't claim it.

## 3 · Tests are the spec

- Write the test **first** (expected input → output); confirm it **fails**; then
  implement to green.
- **Never** delete, skip, weaken an assertion in, or over-mock a test to pass. Test
  genuinely wrong? Say so and ask.
- Pyramid: many unit, fewer integration, fewest e2e. **Golden/snapshot** tests for
  serialized output (query results, plans, schemas).
- A human or fresh-context reviewer confirms a generated test checks the *intended*
  behavior.

## 4 · Change small

- **One logical change per PR** — small enough that a reviewer can read it thoroughly
  in one sitting. Too large to review well? Split it.
- Short-lived branch off `main`, merged in a day or two. Keep `main` releasable; if a
  merge breaks trunk, **revert first**, fix forward.
- Self-contained: everything to understand it is in the diff, its description,
  existing code, or an already-merged PR.

## 5 · Commits, PRs, review

- **Conventional Commits** + **DCO sign-off** (`-s`): `fix:`→patch, `feat:`→minor,
  `!`/`BREAKING CHANGE:`→major — drives SemVer + changelog mechanically.
- PR body: *what & why*, how tested, breaking changes + migration, linked issue/RFD.
- Two-tier review (once `CODEOWNERS` lands): a **reviewer** checks correctness/tests;
  an **approver** checks fit/compat/API. Both required; an owner of every touched
  area approves.

## 6 · Definition of Done

- [ ] tests added/updated, full suite green
- [ ] format + lint + type-check clean
- [ ] docs/reference updated **in the same PR**
- [ ] Conventional-Commit message + changelog + sign-off
- [ ] no stray `TODO`/`FIXME`, no dead code, nothing out-of-scope changed
- [ ] reviewer **and** approver sign-off; CI green on `main` after merge

## 7 · NEVER (hard red lines — enforced by hooks/CI, not trust)

- **Never commit secrets / keys / `.env`** → reference a CI/vault variable and stop.
- **Never bypass CI or hooks** (`--no-verify`, skipping checks). A red gate is a
  signal to fix.
- **Never delete, skip, or weaken tests** to go green (§3).
- **Never force-push or rewrite history** on `main`, `staging`, or release branches.
- **Never swallow an error or add a silent fallback** (§8).
- **Never broaden scope** — touch only what the task needs; surface unrelated issues
  separately.

## 8 · Fail loudly (elaborates [AGENTS.md §0](../AGENTS.md))

A masked error in a data system corrupts state: returning `[]` when the store is
unreachable is indistinguishable from "no rows."

- Validate inputs at boundaries and config at **startup**, not first use.
- On an unhandled condition, **raise an explicit error** — never substitute a
  plausible default. `catch`-log-continue is a silent-failure factory: handle
  meaningfully or re-throw.
- Tool/subprocess/engine errors **propagate verbatim** — never reshaped into success.

## 9 · Security red lines

- **Parameterize every query** — never string-concatenate SQL/queries.
- Treat external input as untrusted; never `eval`/exec it (OWASP LLM05).
- AI features are **prompt-injection** surfaces (OWASP LLM01): least-privilege tools,
  human approval for high-risk actions, and never let model output flow unvalidated
  into a shell, query, or markup.
- **Pin and lock all dependencies.**

## 10 · Don't trust generated code blindly

- **Verify every new symbol, import, and package against real docs** — ~20% of
  LLM-suggested packages don't exist (slopsquatting). Add only vetted deps.
- Plausible-but-wrong passes the linter and fails at runtime — which is why §3 (a
  failing-then-passing test) is mandatory for behavioral changes.
- **Reuse over copy-paste**; abstract on the *third* occurrence, never speculatively.

## Deferred (on purpose)

- **Exact commands** (build/test/lint/run) land here when the language/runtime is
  chosen — the highest-value section, so it's a placeholder, not a guess.
- **Enforcement** (pre-commit hooks + `docs-ci.yml` + branch protection + `CODEOWNERS`)
  is sequenced per RFD-0001; until then these hold by review.
