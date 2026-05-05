# Agent Context Standard

> A standardized way to share project context with AI coding agents — portable, harness-agnostic, token-efficient.

> 🇬🇧 English document. Spanish version: [Agent_Context_Standard_v1.0.es.md](Agent_Context_Standard_v1.0.es.md).
> Formal spec ID: `ACS-001-agent-context-standard` · v1.0 · 2026-05-05.

---

## What is ACS?

The Agent Context Standard (ACS) is a lightweight, open format for storing the context an AI coding agent needs to resume work on a project. It complements the [Agent Skills](https://agentskills.io) format: where Agent Skills capture portable *capabilities*, ACS captures portable *project context* — long-lived facts, research, session state, decisions, and specifications.

At its core, an ACS-compliant project contains a single context directory:

```
my-project/
├── llm-session/                # ← THE single directory holding all agent context
│   ├── MANIFEST.yaml           # Required: schema version + project metadata
│   ├── BOOT.md                 # Required: universal entry point
│   ├── memory/                 # Long-lived facts, rules, conventions
│   ├── knowledge/              # Research dossiers, citable real-world data
│   ├── state/                  # Session snapshots, "where we left off"
│   ├── skills/                 # Reusable Agent Skills (per agentskills.io)
│   ├── specs/                  # Spec-Driven Development artifacts
│   └── decisions/              # Architecture Decision Records (Nygard)
├── CLAUDE.md / GEMINI.md / .cursorrules / AGENTS.md   # One-line harness shims
└── ...                         # Your project files
```

`cp -r` that directory to another machine and the next agent session resumes with full context.

## Why ACS?

Each agent harness today stores "memory" in its own format and location:

* Claude Code → `CLAUDE.md` or `~/.claude/projects/<sanitized-path>/memory/`
* Cursor → `.cursorrules` or `.cursor/rules/*.mdc`
* Gemini CLI → `GEMINI.md`
* Antigravity → `AGENTS.md`
* Continue.dev → `.continue/system.md`

The result: **knowledge is hostage to the harness and the machine.** Switch tools or copy the project elsewhere and continuity is lost.

ACS fixes this:

* **Portable**: every artifact lives inside the project. No global state.
* **Harness-agnostic**: any compatible tool reads the same directory through a one-line root shim.
* **Token-efficient**: agents lazy-load via INDEX → frontmatter → body. No paraphrased summaries, no fidelity loss.
* **Versioned**: `MANIFEST.yaml.acs_version` declares the schema; harnesses ignore unknown extensions safely.
* **Spec-Driven Development built in**: every change traces back to a numbered, immutable-once-accepted spec.

## How does ACS work?

ACS extends the Agent Skills format (used for `skills/`) with five additional artifact types and a discovery protocol. Agents load context in stages:

1. **Discovery**: at startup, the harness reads its native shim at the project root (`CLAUDE.md`, etc.) which links to `llm-session/BOOT.md`.
2. **Boot**: BOOT.md declares the load order; the agent reads `MANIFEST.yaml` and the `INDEX.md` of each subdirectory.
3. **Activation**: when a task matches the description in an INDEX bullet, the agent reads the matching file's full body.
4. **Persistence**: at session end, the agent writes a snapshot under `state/` so the next session resumes from there.

INDEX files quote the frontmatter `description` of every file they index — never a paraphrase of the body. This is the lazy-load contract that bounds token use without sacrificing fidelity.

## Where can I use ACS?

Any agent harness that can read files works. The standard ships shim templates and discovery conventions for:

* Claude Code (`CLAUDE.md`)
* Gemini CLI (`GEMINI.md`)
* Cursor (`.cursorrules`)
* Antigravity (`AGENTS.md`)
* Continue.dev (`.continue/system.md`)

For any other harness, create a root shim with the file name your tool auto-discovers and link it to `llm-session/BOOT.md`.

## Open development

The Agent Context Standard format was originally developed by [CONFIANZA23](https://www.confianza23.es), released as an open standard, and is being adopted by a growing number of agent products. The standard is open to contributions from the broader ecosystem.

ACS builds directly on the [Agent Skills](https://agentskills.io) open format published by Anthropic — the `skills/` directory inside any ACS project is, byte for byte, an Agent Skills directory. ACS adds five sibling directories (`memory/`, `knowledge/`, `state/`, `specs/`, `decisions/`) and a discovery protocol (`MANIFEST.yaml`, `BOOT.md`, `HARNESS.yaml`) for the surrounding project context.

Inspirations:

* **[Agent Skills](https://agentskills.io)** — the `skills/` format ACS reuses verbatim.
* **Michael Nygard, "Documenting Architecture Decisions" (2011)** — `decisions/` ADR format.
* **Karl Wiegers, *Software Requirements* (3rd ed.)** — Spec-Driven Development discipline.
* **Unix dotfile convention** (`.git`, `.vscode`) — canonical name `.agent/`.
* **[AGENTS.md](https://agents.md)** — emerging multi-agent convention adopted as one of the ACS shims.

## Get started

The fastest path is the bundled framework skill:

```bash
# From an existing ACS project, copy the framework bundle
cp -r skills.ACS_Framework_v1.0 /path/to/new-project/

# In the new project, run /init in your harness, then say:
"load ACS"   # or  "carga ACS"
```

The bundled `ACS.FRAMEWORK.agent-context-standard` skill detects whether the project already has ACS, creates the canonical layout if not, and reports compliance.

For manual install, see [Adoption guide](#adoption-guide) below.

---

# Specification

> The complete format specification for Agent Context Standard v1.0.

## Directory structure

ACS defines two acceptable canonical names; harnesses must search the project root in this order:

1. `.agent/` — preferred canonical name (Unix dot-dir convention).
2. `llm-session/` — visible alias.

`MANIFEST.yaml.directory.aliases` permits project-specific additional names.

### Subdirectories

| Subdirectory | Purpose | Required |
|---|---|---|
| `memory/` | Long-lived facts, user preferences, conventions | If populated |
| `knowledge/` | Research dossiers, verified data, citations | Optional |
| `state/` | Ephemeral session snapshots | Optional |
| `skills/` | Reusable [Agent Skills](https://agentskills.io) | Optional |
| `specs/` | Spec-Driven Development artifacts | Optional |
| `decisions/` | Architecture Decision Records | Optional |

All subdirectories are optional. Subdirectories unknown to the harness are silently ignored (forward compatibility).

**Maximum depth**: 3 levels below the canonical directory.
**Exception**: `skills/<name>/` internals follow [Agent Skills](https://agentskills.io/specification) format and may use any internal layout (`references/`, `scripts/`, `assets/`).

## `MANIFEST.yaml` format

The `MANIFEST.yaml` file at the canonical directory root declares schema version, project metadata, and the load order. It must be valid YAML.

### Required fields

| Field | Constraints |
|---|---|
| `acs_version` | Schema version. Currently `"1.0"`. |
| `kind` | Must be `agent-context`. |
| `project.name` | Project name. |
| `project.build` | Project version (semver or build number). |
| `directory.canonical` | One of `llm-session` or `.agent`. |
| `boot.entry` | Entry document, typically `BOOT.md`. |
| `boot.load_order` | Ordered list of subdirectories to read at boot. |

### Optional fields

| Field | Description |
|---|---|
| `project.description` | Max 200 chars. Project description. |
| `project.language` | Default content language (`en`, `es`, ...). |
| `directory.aliases` | Additional directory names accepted. |
| `agents.primary` | The harness this directory was authored for. |
| `agents.compatible` | List of supported harnesses. |
| `boot.required_indexes` | INDEX files that MUST exist or boot fails. |
| `boot.glob_reads` | Glob patterns read at boot for header info only. |
| `schemas` | Per-artifact schema versions. |
| `invariants` | Project-specific binding rules (free-form list). |
| `extensions` | Project-defined; harnesses MUST ignore unknown. |

### Example

```yaml
acs_version: "1.0"
kind: agent-context

project:
  name: my-project
  build: "1.0.0"
  description: "Short project description, ≤200 chars"
  language: en

directory:
  canonical: llm-session
  aliases: [.agent]

agents:
  primary: claude-code
  compatible: [claude-code, gemini-cli, cursor, antigravity, continue-dev]

boot:
  entry: BOOT.md
  load_order: [memory, skills, specs, decisions, knowledge, state]
  required_indexes: [memory/INDEX.md, skills/INDEX.md]
```

## `BOOT.md` format

`BOOT.md` is the universal entry point. Even harnesses that auto-load their native shim must link to `BOOT.md` from that shim.

### Required sections (in order)

1. **Identity** — project name, build, ACS version
2. **Storage policy** — single-source rule, two exceptions only
3. **Load order** — duplicates `MANIFEST.yaml.boot.load_order` for human readability
4. **Invariants** — non-negotiable project rules
5. **Where to start** — pointer to the most recent `state/session_*.md`

### Example

```markdown
---
acs_version: "1.0"
kind: boot
project: my-project
build: "1.0.0"
updated: 2026-05-05
---

# BOOT — my-project · build 1.0.0

## Identity
- Project name, build, domain.

## Storage policy
All session artifacts live inside the canonical directory. Two exceptions: harness shim + harness config.

## Load order
1. MANIFEST.yaml
2. memory/INDEX.md + listed memories
3. ...

## Invariants
- Project-specific binding rules.

## Where to start
Pointer to the most recent state snapshot.
```

## `HARNESS.yaml` format

Optional file declaring per-harness shim mappings. A multi-harness compliant project (compliance level L2+) ships `HARNESS.yaml` plus ≥2 entry shims at the project root.

### Fields

| Field | Required | Description |
|---|---|---|
| `acs_version` | Yes | Schema version, `"1.0"` |
| `kind` | Yes | Must be `harness-shims` |
| `harnesses.<id>.entry_shim` | Yes | Path of the harness's root entry file |
| `harnesses.<id>.skills_path` | No | Where this harness expects to find skills |
| `harnesses.<id>.skills_paths` | No | List of skill discovery paths (for projects with multiple skill bundles) |
| `harnesses.<id>.settings` | No | Path to harness configuration file |
| `harnesses.<id>.notes` | No | Free-form notes |

### Example

```yaml
acs_version: "1.0"
kind: harness-shims

harnesses:
  claude-code:
    entry_shim: CLAUDE.md
    skills_path: skills
    settings: .claude/settings.local.json
  gemini-cli:
    entry_shim: GEMINI.md
    skills_path: skills
  cursor:
    entry_shim: .cursorrules
```

## `INDEX.md` format

Each populated subdirectory must contain an `INDEX.md`. The INDEX is the only thing a fresh agent reads to know what's in that subdirectory.

```markdown
---
kind: index
subdir: <name>
acs_version: "1.0"
---

# <subdir-name> index — <project name>

- [<filename-without-ext>](<filename>) — <description copied verbatim from frontmatter, ≤150 chars>
- ...
```

### Constraints

* One bullet per file
* Description copied **verbatim** from the indexed file's frontmatter `description` — never paraphrased
* Hard cap: 200 lines per `INDEX.md`

This is the lazy-load contract: agent reads INDEX → frontmatter → body, stopping at whichever level answers the question.

## Frontmatter cheat sheet

Every Markdown file under the canonical directory must carry a YAML frontmatter block. Required fields by type:

### `memory/<name>.md`

```yaml
---
name: <kebab-case>           # required, matches filename
description: <≤200 chars>    # required, used for relevance gating
type: user|feedback|project|reference   # required
created: YYYY-MM-DD          # required
updated: YYYY-MM-DD          # required
---
```

### `knowledge/<name>.md`

```yaml
---
name: <kebab-case>
description: <≤200 chars>
asof: YYYY-MM-DD             # date the data was current
sources: [url1, url2]        # verifiable citations
---
```

### `skills/<name>/SKILL.md`

[Agent Skills](https://agentskills.io/specification) format. Minimum `name` and `description`. ACS extensions:

```yaml
---
name: <kebab-case>
description: <when to invoke; trigger phrasing>
version: 1                   # optional; bump on changelog
status: active|superseded|deprecated   # optional
superseded_by: <name>        # if status: superseded
validation: <one line>       # optional; what proves it works
---
```

### `specs/<ID-slug>/SPEC.md`

```yaml
---
id: <ID>                     # required, immutable
title: <human-readable>
status: draft|proposed|accepted|implemented|superseded|rejected
owner: <email or handle>
target_version: <semver>
created: YYYY-MM-DD
updated: YYYY-MM-DD
depends_on: [other-spec-ids]
implements: [other-spec-ids]
---
```

### `decisions/ADR-<NNN>-<slug>.md`

```yaml
---
id: ADR-NNN
title: <human-readable>
status: proposed|accepted|deprecated|superseded
date: YYYY-MM-DD
deciders: [name1, name2]
context_specs: [spec-ids]
---
```

## Progressive disclosure

Agents load ACS context progressively, pulling in more detail only as a task calls for it:

1. **Manifest** (~50 tokens): `MANIFEST.yaml` is read at boot.
2. **Indexes** (≤ 200 lines each): one `INDEX.md` per subdirectory at boot, summarizing metadata only.
3. **Frontmatter** (~10 lines per file): when an INDEX bullet looks relevant, the agent reads the indexed file's frontmatter.
4. **Body** (full content): only when the frontmatter confirms relevance.

Keep individual files focused. Recommended caps:

* `INDEX.md` ≤ 200 lines
* `SKILL.md` body ≤ 250 lines (matches Agent Skills recommendation of < 500 lines / < 5000 tokens)
* `SPEC.md` body ≤ 500 lines
* Larger documents must split into `NOTES.md` or sub-specs

## File references

When referencing other files, use relative paths from the file's own location:

```markdown
See [the data sources memory](../memory/data-sources.md) for the rule.
Run the verifier in [`specs/ACS-001/TESTS.md`](../../specs/ACS-001-agent-context-standard/TESTS.md).
```

Keep references one level deep when possible. Avoid deeply nested reference chains.

## Naming conventions

| Pattern | Example | Use |
|---|---|---|
| `UPPERCASE.md` / `UPPERCASE.yaml` | `BOOT.md`, `MANIFEST.yaml`, `SPEC.md` | Reserved structural files |
| `kebab-case.md` | `data-sources.md`, `lme-cu-cash.md` | Content files |
| `<prefix>-<NNN>-<slug>` | `ACS-001-agent-context-standard`, `ADR-007-vendor-lockin` | Spec and ADR IDs |
| `session_<ISO-DATE>.md` | `session_2026-05-05.md` | Dated state snapshots (ISO 8601) |
| `research_<ISO-DATE>.md` | `research_2026-05-04.md` | Dated knowledge dossiers |

ID prefixes: `ACS-` (the standard's own specs), `ADR-` (Nygard ADRs), `<PROJ>-<VER>-` (project-specific).

IDs are immutable — never reused, even after a spec is deleted.

## Compliance levels

ACS defines five incremental adoption levels. A project may start at L0 and progress.

| Level | Requirements |
|---|---|
| **L0** | Canonical directory + `MANIFEST.yaml` |
| **L1** | + `BOOT.md` + all required `INDEX.md` |
| **L2** | + `HARNESS.yaml` + ≥2 entry shims at project root |
| **L3** | + `specs/` populated with at least one accepted spec for current work |
| **L4** | + `decisions/` populated with ADRs for non-trivial architectural choices |

## Adoption guide

### Case A — fresh project

```bash
mkdir -p llm-session/{knowledge,memory,state,skills,specs,decisions}
# Drop in MANIFEST.yaml, BOOT.md, HARNESS.yaml from templates/
# Add CLAUDE.md (or other harness shim) at project root
```

### Case B — bundled installer (recommended)

The simplest path uses the framework bundle:

```bash
cp -r skills.ACS_Framework_v1.0 /path/to/new-project/
cd /path/to/new-project
/init                    # in your harness
# then say "load ACS" or "carga ACS"
```

The bundled `ACS.FRAMEWORK.agent-context-standard` skill performs the install procedure end to end.

### Case C — migration from another layout

A project with prior `claude-memory/`, `agent-state/`, or similar directories migrates as follows:

1. Create the canonical directory with `MANIFEST.yaml` declaring `aliases: [<old-name>]`.
2. Move existing content into the matching subdirectory (memories → `memory/`, dossiers → `knowledge/`, snapshots → `state/`, skills → `skills/`).
3. Add `INDEX.md` to each subdirectory (one bullet per file, frontmatter description verbatim).
4. Add `BOOT.md` with the load-order section.
5. Update the harness shim (`CLAUDE.md`, etc.) to point to the new `BOOT.md`.
6. Run the conformance verifier (next section). It must pass with no errors.

## Conformance verifier

Reference bash verifier:

```bash
# Required files
test -f llm-session/MANIFEST.yaml || echo "MISSING MANIFEST.yaml"
test -f llm-session/BOOT.md       || echo "MISSING BOOT.md"

# Frontmatter on every .md (skill internals exempt)
find llm-session -name '*.md' \
  -not -path 'llm-session/skills/*/reference/*' \
  -not -path 'llm-session/skills/*/templates/*' \
  -not -path 'llm-session/skills/*/scripts/*' \
  | while read f; do
      head -1 "$f" | grep -q '^---$' || echo "MISSING FRONTMATTER: $f"
    done

# INDEX present per populated subdir
for sub in llm-session/*/; do
  [ "$(ls -A $sub)" ] && [ ! -f "$sub/INDEX.md" ] && echo "MISSING INDEX: $sub"
done

# INDEX size cap (≤ 200 lines)
for f in $(find llm-session -name 'INDEX.md'); do
  n=$(wc -l < "$f")
  [ "$n" -gt 200 ] && echo "INDEX TOO LONG: $f ($n lines)"
done

# Max depth 3 (skill internals exempt)
find llm-session -mindepth 4 -type f -not -path 'llm-session/skills/*' \
  | head -1 | grep . && echo "FILES TOO DEEP"
```

No output = at least L1 compliance.

## Validation tooling

A reference installer ships with this standard: the `ACS.FRAMEWORK.agent-context-standard` skill (bundled inside `skills.ACS_Framework_v<X>.<Y>/`). It detects existing installations, creates the canonical layout from templates, copies this README to the project for human reference, and runs the verifier above. See its `SKILL.md` for the full procedure.

## License

ACS-1.0 is published under [CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/) — use it, fork it, adapt it freely, attributing the origin.

## Origin

Authored by [CONFIANZA23](https://www.confianza23.es) during the development of the STRATOS platform for Navantia (May 2026). The first L4-conforming project is STRATOS itself. ACS is now maintained as an open standard and contributions from any party are welcome.

---

*Agent Context Standard v1.0 · 2026-05-05*
