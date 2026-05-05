---
name: ACS.adr
description: Use when about to make a non-trivial architectural choice that future readers will want the rationale for, or when the user asks "document this decision" / "write an ADR". Produces an Architecture Decision Record in Michael Nygard's format under `decisions/ADR-<NNN>-<slug>.md`. Compatible with ACS-1.0 but works in any project with a `decisions/` directory. Triggers on phrases like "ADR", "architectural decision", "document the why", "record this choice".
license: CC-BY-4.0
metadata:
  version: "1"
  validation: "Used to author ADR-001 (llm-session naming), ADR-002 (single-HTML no-build), ADR-003 (no lossy summaries) in STRATOS v006 on 2026-05-05."
---

# Architecture Decision Records (ADR) — Nygard format

## When to write an ADR

Write one when the choice is:
- **Non-obvious in retrospect** — a future reader (or future you) might revisit it without knowing why.
- **Hard to undo** — switching frameworks, picking a directory layout, choosing a data format.
- **Trade-off-laden** — there's a real con, not just a pro.
- **Cross-cutting** — affects more than one module / team / spec.

Don't write one for:
- Style choices a linter could catch.
- Decisions already captured by an accepted SPEC.
- Reversible refactors.

If the choice is trivial → no ADR. If it's contractual → it's a SPEC, not an ADR. ADRs are for *architectural choices that shape implementation*.

## File location and naming

```
decisions/
├── INDEX.md
└── ADR-<NNN>-<kebab-slug>.md
```

- Number sequentially per project, zero-padded to 3 digits (`ADR-001`, `ADR-042`).
- Slug describes the choice in 3–5 words (`adr-002-single-html-no-build`).
- Never reuse a number, even after deletion. Numbers are immutable identifiers.

## Template (Nygard, lightly adapted)

```markdown
---
id: ADR-<NNN>
title: <human-readable, ≤80 chars>
status: proposed       # proposed → accepted → deprecated → superseded
date: YYYY-MM-DD
deciders: [name1, name2]
context_specs: [<spec-IDs that this decision affects or implements>]
---

# ADR-<NNN> — <title>

## Context
What is the situation that demands a choice? State the constraints and the
forces in tension. One paragraph; no fluff.

## Decision
The actual choice, in one sentence at the top, then expanded with
rationale-bearing detail. Use active voice: "we will X" not "X should be done".

## Consequences
- **Pro**: <consequence>
- **Pro**: <consequence>
- **Con**: <consequence>
- **Con**: <consequence>

(Be honest about cons. ADRs without cons are marketing, not engineering.)

## Alternatives considered (optional but recommended)
- <Alt 1>: rejected because <reason>.
- <Alt 2>: rejected because <reason>.

## When to revisit (optional)
Tripwires that should trigger reconsidering this ADR (e.g. "if file size
exceeds 6k lines, switch to a build pipeline"). Helps future you know
when this decision has expired.
```

## Status state machine

```
proposed ─► accepted ─► deprecated   (no longer recommended; left for context)
                  │
                  └──► superseded    (a newer ADR-NNN replaced it)
```

When an ADR is superseded:
1. Old ADR: `status: superseded`. Add a header line `Superseded by ADR-NNN`.
2. New ADR: include the old in `context_specs:` and explain why the choice changed.
3. INDEX.md keeps both with status badges.

## Authoring procedure

1. Identify the choice. Is it ADR-worthy (per the list above)?
2. Pick the next ADR number: `ls decisions/ADR-*.md | sort -V | tail -1` then increment.
3. Draft ADR with `status: proposed`.
4. Show to deciders → flip to `accepted` (or `rejected` and don't write).
5. Add to `decisions/INDEX.md`: `- [ADR-NNN-slug](ADR-NNN-slug.md) — title.`
6. Reference from any related SPEC's `depends_on:` or NOTES.md.

## Anti-patterns

- **Multiple decisions in one ADR**. One choice per ADR. If you're tempted, split.
- **No cons listed**. Means you didn't think it through; reviewer can't trust the choice.
- **Vague title**. "ADR-007: storage" → bad. "ADR-007: choose JSON over CBOR for user backups" → good.
- **Implementation steps in the ADR body**. ADRs explain *why*, not *how*. The how lives in commits and SPEC implementations.
- **Deleting old ADRs**. Mark them superseded; never delete. The ID is permanent.

## INDEX.md format

```markdown
---
kind: index
subdir: decisions
acs_version: "1.0"
---

# decisions index — <project>

- [ADR-001-<slug>](ADR-001-<slug>.md) — <title>. status: accepted.
- [ADR-002-<slug>](ADR-002-<slug>.md) — <title>. status: superseded by ADR-007.
```

## Cap on ADR length

Keep ADR bodies ≤ 200 lines. If you need more, split the topic into multiple ADRs or write a SPEC. Long ADRs are usually trying to be design docs — that's a different artifact.

## Validation

Authored 2026-05-05 to record three architectural choices in STRATOS v006:
- ADR-001 — `llm-session/` as canonical alias of ACS `.agent/` (per-user preference).
- ADR-002 — single-HTML SPA with Babel Standalone, no build step.
- ADR-003 — INDEX files summarise frontmatter only, never lossy summaries of bodies.

Each carries Context / Decision / Consequences / Alternatives / When-to-revisit; all under 100 lines; INDEX.md gives one-line status per ADR.
