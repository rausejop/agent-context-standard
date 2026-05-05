---
name: ACS.FRAMEWORK.agent-context-standard
description: Bootstrap, transplant or refresh ACS-1.0 (Agent Context Standard) in any project. Self-contained — the skill folder bundles Agent_Context_Standard_v1.0.es.md/.en.md and all required templates under references/, so dropping just this folder into a new project's .claude/skills/ (or anywhere reachable) and saying "load ACS" installs the full methodology. Trigger phrases (any language, any harness) — "load ACS", "carga ACS", "init ACS", "carga la metodología ACS", "instala ACS", "bootstrap ACS", "set up agent context standard", "transplant llm-session".
license: CC-BY-4.0
metadata:
  version: "3"
  validation: "STRATOS v006 (2026-05-05): self-applied successfully producing L4-conformant llm-session/. v3 makes the skill self-contained: references/Agent_Context_Standard_v1.0.es.md and references/Agent_Context_Standard_v1.0.en.md are bundled as canonical, and references/templates/ holds every structural file (MANIFEST, BOOT, HARNESS, INDEX, the four harness shims), so the entire methodology is portable by copying ONLY this folder."
---

# Agent Context Standard (ACS-1.0) — portable installer

## What this skill does

When the user says any trigger phrase (see frontmatter `description`), this skill:

1. **Detects** whether the project already has an ACS structure (`llm-session/MANIFEST.yaml` or `.agent/MANIFEST.yaml`).
2. **If absent** — creates the full canonical layout from the templates bundled under `references/templates/`, copies the canonical READMEs from `references/` to the project root, places the harness shims at the project root, and moves this skill into `llm-session/skills/skills.ACS.FRAMEWORK.agent-context-standard/` so future sessions auto-discover it.
3. **If present** — refreshes shims, regenerates `skills/INDEX.md`, re-syncs the project-root READMEs from the bundled canonical, runs the conformance verifier, and reports compliance level.

After either path, the project is L1+ conformant and the user can start working with the full methodology in place.

## Self-contained portability promise

Everything needed to install ACS in another project lives **inside this skill folder**:

```
skills.ACS.FRAMEWORK.agent-context-standard/
├── SKILL.md                       # this file (procedure + triggers)
└── reference/
    ├── Agent_Context_Standard_v1.0.es.md              # canonical Spanish standard reference (615 lines)
    ├── Agent_Context_Standard_v1.0.en.md           # canonical English standard reference (615 lines)
    └── templates/
        ├── MANIFEST.yaml          # llm-session/MANIFEST.yaml template
        ├── BOOT.md                # llm-session/BOOT.md template
        ├── HARNESS.yaml           # llm-session/HARNESS.yaml template
        ├── INDEX.md               # template for llm-session/<sub>/INDEX.md
        ├── CLAUDE.md              # project-root Claude Code shim template
        ├── GEMINI.md              # project-root Gemini CLI shim template
        ├── .cursorrules           # project-root Cursor shim template
        └── AGENTS.md              # project-root Antigravity shim template
```

**Distribution = copy this folder.** No external dependencies, no fetched URLs, no version drift.

## Cold-start procedure (new project, never had ACS)

Pre-condition: the user has run `/init` in Claude Code (which produces a basic root `CLAUDE.md`), then dropped this skill folder somewhere Claude can find it (typical: `.claude/skills/skills.ACS.FRAMEWORK.agent-context-standard/`), then said one of the trigger phrases.

### Step 1 — locate this skill folder

The skill might be at `.claude/skills/skills.ACS.FRAMEWORK.agent-context-standard/`, `~/.claude/skills/skills.ACS.FRAMEWORK.agent-context-standard/`, or anywhere else. Use Glob `**/skills.ACS.FRAMEWORK.agent-context-standard/SKILL.md` to find it. Define `SKILL_DIR` as the parent of that match. The `references/` and `references/templates/` directories are alongside.

### Step 2 — detect existing ACS structure

```bash
test -f llm-session/MANIFEST.yaml && echo "EXISTS:llm-session"
test -f .agent/MANIFEST.yaml && echo "EXISTS:.agent"
```

If either prints `EXISTS:...`, jump to "Refresh procedure" below.

### Step 3 — gather identity from the user

Ask the user (or infer from existing project files like `package.json`, `README.md`, top-level filenames):
- `<PROJECT_NAME>` (e.g. STRATOS)
- `<VERSION>` (e.g. 006, 1.0.0)
- `<DESCRIPTION>` — one line, ≤200 chars
- `<DOMAIN>` — what the project does
- Default `<LANGUAGE>` (en/es)

### Step 4 — create the canonical layout

```bash
mkdir -p llm-session/{knowledge,memory,state,skills,specs,decisions}
```

### Step 5 — instantiate from bundled templates

Read each template under `$SKILL_DIR/references/templates/`, substitute placeholders, write to the destination:

| Template (in `references/templates/`) | Destination | Substitutions |
|---|---|---|
| `MANIFEST.yaml` | `llm-session/MANIFEST.yaml` | `<PROJECT_NAME>`, `<VERSION>`, `<DESCRIPTION>`, `<LANGUAGE>` |
| `BOOT.md` | `llm-session/BOOT.md` | `<PROJECT_NAME>`, `<VERSION>`, `<YYYY-MM-DD>` (today), one-line description, domain |
| `HARNESS.yaml` | `llm-session/HARNESS.yaml` | (none — generic) |
| `INDEX.md` | `llm-session/{memory,knowledge,state,skills,specs,decisions}/INDEX.md` | `<SUBDIR_NAME>`, `<PROJECT_NAME>`, today's date — write one per subdir |
| `CLAUDE.md` | `CLAUDE.md` (project root, replacing the basic `/init` output) | `<PROJECT_NAME>`, `<VERSION>`, description, domain |
| `GEMINI.md` | `GEMINI.md` (project root) | same |
| `.cursorrules` | `.cursorrules` (project root) | same |
| `AGENTS.md` | `AGENTS.md` (project root) | same |

### Step 6 — copy bundled READMEs to project root

```bash
cp "$SKILL_DIR/references/Agent_Context_Standard_v1.0.es.md"    ./Agent_Context_Standard_v1.0.es.md
cp "$SKILL_DIR/references/Agent_Context_Standard_v1.0.en.md" ./Agent_Context_Standard_v1.0.en.md
```

These root copies are derived artefacts (humans browsing the project find them at the root); the canonical text lives in `references/`.

### Step 7 — relocate this skill into the canonical skills dir

If the skill folder is not already at `llm-session/skills/skills.ACS.FRAMEWORK.agent-context-standard/`, move it there:

```bash
mkdir -p llm-session/skills
mv "$SKILL_DIR" llm-session/skills/skills.ACS.FRAMEWORK.agent-context-standard
```

(If moving from `.claude/skills/` you may prefer to leave a copy and just `cp -r`; either works.)

### Step 8 — verify

Run the verifier from `references/Agent_Context_Standard_v1.0.es.md` (Conformance verifier section). All five checks must produce no output for L1 compliance.

```bash
test -f llm-session/MANIFEST.yaml || echo "MISSING MANIFEST.yaml"
test -f llm-session/BOOT.md       || echo "MISSING BOOT.md"
find llm-session -name '*.md' -not -path 'llm-session/skills/*/reference/*' -not -path 'llm-session/skills/*/templates/*' -not -path 'llm-session/skills/*/scripts/*' | while read f; do
  head -1 "$f" | grep -q '^---$' || echo "MISSING FRONTMATTER: $f"
done
for sub in llm-session/*/; do
  [ "$(ls -A "$sub")" ] && [ ! -f "${sub}INDEX.md" ] && echo "MISSING INDEX: $sub"
done
for f in $(find llm-session -name 'INDEX.md'); do
  n=$(wc -l < "$f"); [ "$n" -gt 200 ] && echo "INDEX TOO LONG: $f ($n)"
done
find llm-session -mindepth 4 -type f -not -path 'llm-session/skills/*' | head -1 | grep . && echo "FILES TOO DEEP"
```

### Step 9 — report

Tell the user: compliance level reached, files created (numbered), where to start next (suggest reading `llm-session/BOOT.md`, then writing the first `state/session_<today>.md`).

## Refresh procedure (ACS already present)

When `llm-session/MANIFEST.yaml` already exists:

1. **Re-sync READMEs**: `diff` `references/Agent_Context_Standard_v1.0.es.md` ↔ `./Agent_Context_Standard_v1.0.es.md` (and the `.en` pair). If the canonical (`references/`) is newer, `cp` to root. If the root is newer, ask the user which to keep — they may have edited intentionally.
2. **Regenerate `skills/INDEX.md`**: glob every `llm-session/skills/*/SKILL.md`, lift each `description` from frontmatter verbatim, rewrite the INDEX. Mark superseded skills under a separate section.
3. **Validate compliance**: run the verifier. Report the level (L0–L4) and any gaps.
4. **Report**: list files refreshed, list nothing if no drift was found.

Do not touch `memory/`, `knowledge/`, `state/`, `specs/`, `decisions/` — those are project content, not standard structure.

## Updating the methodology itself

When ACS evolves (e.g. v1.1 adds an `assets/` subdir, or a new schema field):

1. Edit `references/Agent_Context_Standard_v1.0.es.md` and `references/Agent_Context_Standard_v1.0.en.md` (canonical source — KEEP IN BILINGUAL PARITY, see [`skills.ACS.docs.bilingual`](../ACS.docs.bilingual/SKILL.md)).
2. Update `references/templates/*` for any structural change.
3. Bump `acs_version` in `references/templates/MANIFEST.yaml` AND in this `SKILL.md` frontmatter, AND in the canonical READMEs.
4. Distribute by copying this `skills.ACS.FRAMEWORK.agent-context-standard/` folder to the next project.

The two project-root READMEs are derived copies; refresh them by re-running this skill.

## Framework bundle layout (binding)

This skill ships as part of a **versioned, read-only framework bundle**: `skills.ACS_Framework_v<MAJOR>.<MINOR>/` at the project root. The bundle contains this skill plus the six ACS methodology utilities (`ACS.adr`, `ACS.spec`, `ACS.update-skills`, `ACS.backup.timestamped`, `ACS.docs.bilingual`, `ACS.docs.standard`).

**Read-only invariant**: never modify any file inside `skills.ACS_Framework_v<X>.<Y>/`. To change the framework:

1. `cp -r skills.ACS_Framework_v1.0 skills.ACS_Framework_v1.draftN` (next available `draftN` slot).
2. Edit freely inside the draft directory.
3. Manually verify (T1–T8, frontmatter, parity, sample bootstrap, real-session test).
4. Promote: rename draft to next stable version (`v1.1`, `v1.2`, ...), update its `VERSION.md`.
5. Update project shims (`CLAUDE.md`, etc.) to reference the new versioned dir.

See the bundle's `VERSION.md` for the full workflow.

**Project skills** (anything that is not framework methodology — e.g. `react.*`, `app.*`, `scrap.*`) live in the project's regular `skills/` directory and evolve freely, unaffected by this read-only invariant.

## Where the bundle can live

Possible installations (any of):
- Project root: `<project>/skills.ACS_Framework_v<X>.<Y>/` — discoverable via project shims (`CLAUDE.md`, `AGENTS.md`, `.cursorrules`).
- User-global: `~/.claude/skills.ACS_Framework_v<X>.<Y>/` — install once, reference from every project's shim.
- Per-harness convention: `.claude/skills/` etc. — possible but loses the bundle structure; not recommended.

**Recommended workflow** for a new project:
1. Run Claude Code's `/init` to produce the basic `CLAUDE.md`.
2. Copy the entire **`skills.ACS_Framework_v<latest>/`** directory (NOT just this skill) to the new project root. The bundle includes this bootstrap skill plus the 6 ACS utility skills, plus `VERSION.md` declaring the read-only invariant.
3. Say "load ACS" / "carga ACS".
4. Done — methodology fully installed; project-evolving skills go under the regular `skills/` directory.

## Compliance levels recap

- **L0** — directory + `MANIFEST.yaml`.
- **L1** — + `BOOT.md` + all required `INDEX.md`.
- **L2** — + `HARNESS.yaml` + ≥2 entry shims at project root.
- **L3** — + at least one accepted spec for current work.
- **L4** — + ADRs for non-trivial architectural choices.

This skill always reaches L2 immediately on cold-start (4 shims, HARNESS.yaml). L3/L4 emerge as the project produces specs and ADRs.

## Token-optimisation invariants (binding)

1. **No lossy summaries** — INDEX bullets quote frontmatter `description`; never paraphrase bodies.
2. **Lazy load** — INDEX → frontmatter → body, stopping at the level that answers the current question.
3. **Frontmatter must be self-sufficient** — fix the description rather than reading the body.
4. **One concept per file** — split oversize memories / skills.
5. **Max depth 3** below the canonical dir.
6. **Caps**: INDEX ≤ 200 lines, SKILL body ≤ 250, SPEC body ≤ 500.

## Validation

Authored 2026-05-05 (v1) and refined to v3 the same day to make the skill self-contained: bundled `references/Agent_Context_Standard_v1.0.es.md/.en.md` (canonical reference) and `references/templates/*` (every structural file required by ACS). Tested by re-bootstrapping the STRATOS v006 layout from these templates — verifier passes with no output (L4 compliance preserved). Portability validated: `cp -r skills.ACS.FRAMEWORK.agent-context-standard/ /other-project/.claude/skills/` + saying "load ACS" produces a fresh L2 install.
