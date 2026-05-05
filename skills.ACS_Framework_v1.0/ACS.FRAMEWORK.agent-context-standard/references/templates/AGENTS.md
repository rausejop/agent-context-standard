# AGENTS.md — multi-agent entry shim (ACS-1.0 compliant)

Universal entry for AGENTS.md-aware harnesses (Google Antigravity et al). Session continuity lives at [`llm-session/`](llm-session/); the real bootstrap is at [`llm-session/BOOT.md`](llm-session/BOOT.md).

## Read first

1. [`llm-session/BOOT.md`](llm-session/BOOT.md) — universal load instructions.
2. [`llm-session/MANIFEST.yaml`](llm-session/MANIFEST.yaml) — schema, project meta.
3. [`llm-session/skills/INDEX.md`](llm-session/skills/INDEX.md) — available skills.

## Storage policy

Every session artifact lives inside `llm-session/`. Exceptions: this file + `.antigravity/config.yaml`.

## Project at a glance

- **App:** <PROJECT_NAME>
- **Build:** <VERSION>
- **Domain:** <project domain>.
