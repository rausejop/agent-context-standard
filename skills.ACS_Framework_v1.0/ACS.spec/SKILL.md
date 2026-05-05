---
name: ACS.spec
description: Use when authoring or accepting a Spec-Driven Development specification — a contract for a piece of work that drives implementation. Triggers on phrases like "write a spec for", "create the SPEC.md", "convert these requirements into a spec", "SDD". Produces a SPEC.md (immutable once accepted) + TESTS.md (acceptance criteria) + STATUS.md (live implementation tracking) under `specs/<ID>-<slug>/`. Compatible with ACS-1.0; works in any project with a `specs/` directory.
license: CC-BY-4.0
metadata:
  version: "1"
  validation: "Used to convert Versión 6.txt and stratos.bugfix.006.txt into STR-006-platform-features and STR-006-bugfixes specs; ACS-001 itself is also expressed in this format."
---

# Spec-Driven Development — authoring SPEC + TESTS + STATUS

## When to invoke

- The user asks for a new spec for in-flight or planned work.
- A `<project>.<version>.txt` requirements file exists in the repo and needs to become a tracked SDD artifact.
- A change is about to be implemented and there is no existing spec for it.
- A spec is about to be marked `accepted` and you need to lock the SPEC body and start tracking via STATUS.

## Layout

```
specs/
└── <ID>-<slug>/
    ├── SPEC.md     # the contract — immutable once status: accepted
    ├── TESTS.md    # acceptance criteria; gives "done" a meaning
    ├── NOTES.md    # mutable scratchpad during implementation (optional)
    └── STATUS.md   # live; per-criterion implementation status
```

ID conventions:
- `<PROJ>-<VER>-<slug>` for project work (e.g. `STR-006-bugfixes`).
- `ACS-NNN` for the Agent Context Standard itself.
- Three-letter project prefix recommended; pad version to match repo's scheme.

## SPEC.md template

```markdown
---
id: <ID>
title: <human-readable, ≤80 chars>
status: draft       # draft → proposed → accepted → implemented → superseded → rejected
owner: <email or handle>
target_version: <semver or build tag>
created: YYYY-MM-DD
updated: YYYY-MM-DD
depends_on: []      # other spec IDs that must be implemented first
implements: []      # other spec IDs this one supersedes
tags: [...]
source: <where the requirements came from — txt file, conversation, ticket>
---

# <ID> — <title>

## Summary
One paragraph: what changes, why now, who needs it.

## REQ00 — Preserve binding rule
First numbered REQ is always the no-regression rule for the affected surface.

## REQ01 — <feature/fix>
Describe in operational terms, not implementation. State observable behaviour.

## REQ02 — ...

(repeat per requirement; keep one concept per REQ)

## Acceptance — see TESTS.md
[TESTS.md](TESTS.md) lists per-REQ acceptance. Implementation status in [STATUS.md](STATUS.md).
```

## TESTS.md template

```markdown
---
spec: <ID>
created: YYYY-MM-DD
---

# <ID> — Acceptance criteria

For each REQ, state the observable test that must pass.

## REQ01
- T01.1: <observable check>
- T01.2: <observable check>

## REQ02
- T02.1: ...

## Verification command (optional)
A reference verifier MAY be implemented as a bash one-liner here so reviewers
can run it locally. Keep it deterministic; no LLM calls.
```

## STATUS.md template

```markdown
---
spec: <ID>
status: draft|in-progress|implemented|blocked
implementer: <claude-code | name>
date: YYYY-MM-DD
---

# <ID> — Implementation status

| REQ | Status | Evidence |
|---|---|---|
| REQ00 preserve | n/a | binding rule |
| REQ01 ... | ✅ | <file:line or grep evidence> |
| REQ02 ... | ⏳ pending | <reason> |
| REQ03 ... | 🚫 blocked | <blocker> |
```

## Status state machine

```
draft ─────► proposed ─────► accepted ─────► implemented
  │              │               │                 │
  │              │               └────► superseded │
  └──────────────┴────────────────────► rejected   │
                                                   │
                                  implemented ◄────┘
```

Once `status: accepted`:
- The SPEC.md body is **immutable**. Further refinements go to NOTES.md.
- Any change in scope requires a new spec with `implements: [<old-id>]` and the old spec moves to `superseded`.
- STATUS.md is the only document allowed to change as work progresses.

## Authoring procedure (token-efficient)

1. **Skim source material** — `Read` the `.txt` requirements file or the conversation context.
2. **Extract REQs** — one bullet per atomic requirement. REQ00 is always the preserve-existing rule.
3. **Draft SPEC.md** with `status: draft`. Use operational phrasing, not implementation prescription.
4. **Draft TESTS.md** in parallel. If you can't write a test, the REQ isn't well-defined yet.
5. **Draft STATUS.md** with all REQs `⏳ pending`.
6. **Show to user → accept** → change `status: accepted` → SPEC body is now locked.
7. **Add to `specs/INDEX.md`** with one line: `- [<ID>-<slug>](<ID>-<slug>/SPEC.md) — <title> · status: <s>`.
8. **Mention in next state snapshot** under `state/session_<DATE>.md`.

## Anti-patterns

- **Edit accepted SPEC bodies** — breaks the contract. Use NOTES.md or a new spec.
- **Mix REQ scope** — "REQ04: refactor X and add Y and fix Z" → split into 3 REQs.
- **Skip TESTS.md** — without acceptance criteria the spec is just a wishlist.
- **Implementation in SPEC.md** — say *what* and *why*, not *how*. The how lives in commits and NOTES.md.
- **Pad with prose** — every paragraph that doesn't change the contract dilutes review.

## Validation

Authored 2026-05-05 to convert STRATOS source requirement files (`Versión 6.txt`, `stratos.bugfix.006.txt`) into `specs/STR-006-platform-features/` and `specs/STR-006-bugfixes/`. The ACS standard itself (`specs/ACS-001-agent-context-standard/`) follows this template. Per-spec STATUS.md tables give a single-glance view of remaining work without reading any SPEC body.
