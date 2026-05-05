---
kind: framework-version
framework: ACS
version: "1.0"
status: stable
read_only: true
released: 2026-05-05
canonical: true
---

# ACS Framework v1.0 — versioned bundle

This directory is the **stable, read-only** distribution of the Agent Context Standard methodology bundle. Copy the entire directory to any new project to install the methodology.

## Contents

7 skills:

- `ACS.FRAMEWORK.agent-context-standard/` — bootstrap / install / refresh entry point. Triggered by "load ACS" / "carga ACS".
- `ACS.adr/` — Architecture Decision Record authoring (Nygard format).
- `ACS.spec/` — Spec-Driven Development authoring (SPEC + TESTS + STATUS).
- `ACS.update-skills/` — re-audits the project's `skills/` and refreshes its INDEX. Respects framework read-only.
- `ACS.backup.timestamped/` — `tsName(base)` helper for `YYYYMMDD_HHMM` exports.
- `ACS.docs.bilingual/` — ES↔EN structural parity for documentation.
- `ACS.docs.standard/` — 16-section anatomy for authoring public open standards.

## Read-only invariant

Files inside `skills.ACS_Framework_v1.0/` MUST NOT be modified in place. Any attempt by `ACS.update-skills` or by an operator to change a framework skill must be redirected to the draft directory (see versioning workflow below).

The only files that may be appended without breaking the invariant are *informational* (e.g. this `VERSION.md` is fixed once released; no editing).

## Versioning workflow

```
skills.ACS_Framework_v1.0/      ← current stable, READ-ONLY, copied to new projects
        │
        ▼  framework needs change → create draft alongside
skills.ACS_Framework_v1.draft1/ ← editable; develop new framework skills here
        │
        ▼  manual verification (review + tests + L4 conformance + bump)
skills.ACS_Framework_v1.1/      ← next stable, READ-ONLY; old v1.0 retained or archived
        │
        ▼  loop
skills.ACS_Framework_v1.draft2/ → skills.ACS_Framework_v1.2/ → ...
```

**Steps to release a new framework version:**

1. **Branch**: `cp -r skills.ACS_Framework_v1.0 skills.ACS_Framework_v1.draft1` (or whatever the next `.draftN` slot is — increment per attempt).
2. **Edit freely** inside the draft directory. All framework changes happen here.
3. **Verify manually**:
   - Conformance verifier (T1–T8 of `specs/ACS-001-*/TESTS.md`) passes for the draft.
   - All 7 skills' SKILL.md frontmatter remains complete and self-consistent.
   - `ACS.FRAMEWORK.agent-context-standard/SKILL.md` bootstrap procedure rebuilds a sample `llm-session/` correctly.
   - Bundled `references/Agent_Context_Standard_v1.0.{es,en}.md` parity check (`wc -l` ≤ 3% delta).
   - At least one user/workflow test (load draft into a sample project, run a real session).
4. **Promote**: rename `skills.ACS_Framework_v1.draftN` → `skills.ACS_Framework_v<NEXT>` (`v1.1`, `v1.2`, ...).
   - Update `VERSION.md.version` and `released` date.
   - Decide whether to delete the old stable directory or keep it for archival (project's call).
5. **Update active pointer**: edit project root `CLAUDE.md` / `AGENTS.md` / `.cursorrules` shims to reference the new versioned directory if they pin a specific version. If they reference by glob (`skills.ACS_Framework_v*` newest), nothing to change.

## What goes in this bundle vs. project skills/

| Lives here (`skills.ACS_Framework_v1.0/`) | Lives in project `skills/` |
|---|---|
| The methodology itself + utilities defined by ACS | Domain skills (React, scraping, app-specific patterns) |
| Stable, versioned, read-only | Evolves freely with the project |
| Copied as a unit to new projects | Stays with the project |
| Naming: `framework.*`, `ACS.*` | Naming: `react.*`, `scrap.*`, `app.*`, etc. |

## Discovery by harnesses

Claude Code / Gemini CLI / Cursor / Antigravity / Continue.dev discover skills inside this bundle via the project shims (`CLAUDE.md`, `AGENTS.md`, etc.) which list both `skills/` and `skills.ACS_Framework_v1.0/` (or any `skills.ACS_Framework_v*/` glob) as discovery paths. Cross-bundle references (e.g. `ACS.update-skills` reading the project's `skills/INDEX.md`) use relative paths from this bundle's location.

## Activation

Saying "load ACS" / "carga ACS" / "init ACS" triggers `ACS.FRAMEWORK.agent-context-standard/SKILL.md`, which performs the cold-start or refresh procedure. See that skill's body for details.
