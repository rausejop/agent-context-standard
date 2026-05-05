# GEMINI.md — Gemini CLI entry shim (ACS-1.0 compliant)

This project follows the **Agent Context Standard (ACS) v1.0**. Session continuity lives at [`llm-session/`](llm-session/). This file is the Gemini CLI shim; the real bootstrap is at [`llm-session/BOOT.md`](llm-session/BOOT.md).

## Read first

1. [`llm-session/BOOT.md`](llm-session/BOOT.md) — universal load instructions.
2. [`llm-session/MANIFEST.yaml`](llm-session/MANIFEST.yaml) — schema, project meta.
3. [`llm-session/skills/INDEX.md`](llm-session/skills/INDEX.md) — available skills.

## Storage policy

Every session artifact lives inside `llm-session/`. Exceptions: this file + `.gemini/config.json`.

## Project at a glance

- **App:** <PROJECT_NAME>
- **Build:** <VERSION>
