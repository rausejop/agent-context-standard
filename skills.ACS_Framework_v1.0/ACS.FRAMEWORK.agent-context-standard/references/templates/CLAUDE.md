# CLAUDE.md — Claude Code entry shim (ACS-1.0 compliant)

This project follows the **Agent Context Standard (ACS) v1.0**. The single source of session truth lives at [`llm-session/`](llm-session/). This file is the harness shim that Claude Code auto-discovers; the real bootstrap is at [`llm-session/BOOT.md`](llm-session/BOOT.md).

## Read first

1. **[`llm-session/BOOT.md`](llm-session/BOOT.md)** — universal load instructions, identity, storage policy, invariants, "where to start" for the current session.
2. **[`llm-session/MANIFEST.yaml`](llm-session/MANIFEST.yaml)** — schema version, project meta, harness compatibility, load order.
3. **[`llm-session/skills/INDEX.md`](llm-session/skills/INDEX.md)** — auto-loadable skills.

## Storage policy (binding)

Every file of knowledge / memory / state / skills / specs / decisions lives ONLY inside `llm-session/`. Two exceptions:

1. This file (`CLAUDE.md`) — Claude Code's native auto-load entry; thin shim only.
2. `.claude/settings.local.json` — minimal harness permissions.

If you find session artifacts anywhere else (legacy `.<project>-*` dirs, top-level `skills/`, global `~/.claude/projects/.../memory/`), migrate them in and delete the source. The transcript JSONL the harness writes is owned by the harness — leave it alone.

## Project at a glance

- **App:** <PROJECT_NAME> — <one-line description>.
- **Build:** <VERSION>.
- **Domain:** <project domain>.

## ACS skills protocol (reminder)

- **Auto-discovery**: every `llm-session/skills/<name>/SKILL.md` surfaces by its `description` frontmatter.
- **ACS bootstrap skill (e.g. `ACS.FRAMEWORK.agent-context-standard`)**: handles ACS install / refresh / transplant. Trigger phrases: "load ACS", "carga ACS", "init ACS".
- **INDEX-regeneration skill (e.g. `ACS.update-skills`)** (when present): re-audits, replaces superseded skills, refreshes `llm-session/skills/INDEX.md`.
- **Validation gate**: only save a skill after the technique was used in-session and the user did not push back.

## Build / run

(Project-specific build/run instructions — fill in.)
