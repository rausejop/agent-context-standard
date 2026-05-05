---
acs_version: "1.0"
kind: boot
project: <PROJECT_NAME>
build: "<VERSION>"
updated: <YYYY-MM-DD>
---

# BOOT — <PROJECT_NAME> · build <VERSION>

Universal entry point for any ACS-1.0 compliant harness. Read this once at the start of every session, then follow the load order below.

## Identity

- **Project:** <PROJECT_NAME> — <one-line description>.
- **Build:** <VERSION>.
- **Domain:** <project domain>.
- **ACS reference:** [`../skills/ACS.FRAMEWORK.agent-context-standard/references/Agent_Context_Standard_v1.0.es.md`](../skills/ACS.FRAMEWORK.agent-context-standard/references/Agent_Context_Standard_v1.0.es.md) (canonical, bundled with the skill).

## Storage policy (binding)

All Claude / agent session continuity lives **inside this directory only**. Two exceptions:

1. The harness entry shim at the project root (e.g. `CLAUDE.md`, `GEMINI.md`, `.cursorrules`, `AGENTS.md`).
2. `.claude/settings.local.json` (or equivalent harness config) — minimal permissions only.

If session artifacts ever appear outside this directory, migrate them in and delete the source. The transcript JSONL the harness writes is owned by the harness — leave it alone.

## Load order

At session start, read in this order. After each step the agent has enough context to start working; deeper reads happen on demand.

1. **`MANIFEST.yaml`** — schema version, project meta, harness compatibility.
2. **`memory/INDEX.md`** + every memory file it lists. Non-negotiable rules.
3. **`skills/INDEX.md`** + the SKILL.md frontmatter of each.
4. **`specs/INDEX.md`** — what work has been agreed; pull a SPEC.md when working on it.
5. **`decisions/INDEX.md`** — past architectural choices.
6. **`knowledge/*.md`** glob — research dossiers; cite for any "real data" claim.
7. **`state/session_*.md`** newest first — where the previous session left off.

## Invariants (project-specific, binding)

(Populate with project-specific binding rules. Examples:)

- "Real data only; cite knowledge/research_*.md or fresh WebSearch."
- "No fabricated quotes."

## Where to start (this session)

(Pointer to the most recent state snapshot. Initially empty — populated after the first session.)
