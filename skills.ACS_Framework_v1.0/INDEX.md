---
kind: index
subdir: skills.ACS_Framework_v1.0
acs_version: "1.0"
framework_version: "1.0"
status: stable
read_only: true
updated: 2026-05-05
---

# ACS Framework v1.0 — bundled skills index

7 skills bundled in this versioned framework distribution. **READ-ONLY** — see [`VERSION.md`](VERSION.md) for the draft → stable promotion workflow.

## Bootstrap entry

- [ACS.FRAMEWORK.agent-context-standard](ACS.FRAMEWORK.agent-context-standard/SKILL.md) — Bootstrap, transplant or refresh ACS-1.0 in any project. Self-contained — bundles Agent_Context_Standard_v1.0.{es,en}.md + all templates under references/, so dropping just this folder + saying "load ACS" / "carga ACS" installs the full methodology. **v3.**

## ACS methodology utilities

- [ACS.update-skills](ACS.update-skills/SKILL.md) — Trigger when user types "actualiza skills" / "update skills"; re-audits, supersedes, adds, removes, refreshes the project `skills/INDEX.md`. ACS-aware: also updates specs/STATUS.md and the latest state snapshot. Respects framework read-only invariant. **v2.**
- [ACS.adr](ACS.adr/SKILL.md) — Use when about to make a non-trivial architectural choice or user asks for "ADR" / "document the why". Produces ADR in Nygard format under `decisions/`. Compatible with ACS-1.0.
- [ACS.spec](ACS.spec/SKILL.md) — Use when authoring an SDD spec; produces SPEC.md (immutable once accepted) + TESTS.md + STATUS.md under `specs/<ID>-<slug>/`. ACS-compatible.
- [ACS.backup.timestamped](ACS.backup.timestamped/SKILL.md) — `tsName(base)` helper that stamps every export with `YYYYMMDD_HHMM`.
- [ACS.docs.bilingual](ACS.docs.bilingual/SKILL.md) — Use for ES+EN (or any 2-language) docs that must stay in lockstep — README, public standards, marketing pages. Enforces structural parity.
- [ACS.docs.standard](ACS.docs.standard/SKILL.md) — Use when designing/publishing a public standard (open spec, RFC, convention). Provides 16-section anatomy.
