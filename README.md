# 🌐 Agent Context Standard (ACS)

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Version](https://img.shields.io/badge/version-1.0-blue.svg)](Agent_Context_Standard_v1.0.en.md)
[![Status: Stable](https://img.shields.io/badge/status-stable-brightgreen.svg)](../../VERSION.md)
[![Built on Agent Skills](https://img.shields.io/badge/built%20on-Agent%20Skills-orange.svg)](https://agentskills.io)
[![Spec ID](https://img.shields.io/badge/spec-ACS--001-purple.svg)](Agent_Context_Standard_v1.0.en.md)

> A standardized way to share project context with AI coding agents — portable, harness-agnostic, token-efficient.

This directory holds the **canonical reference** for ACS v1.0. Pick your language below, or jump to the bundled templates.

---

## 🌍 Read the standard

| Language | Document | Role |
|---|---|---|
| 🇬🇧 **English** | [`Agent_Context_Standard_v1.0.en.md`](Agent_Context_Standard_v1.0.en.md) | **Canonical** — the source of truth |
| 🇪🇸 **Español** | [`Agent_Context_Standard_v1.0.es.md`](Agent_Context_Standard_v1.0.es.md) | Translation, line-for-line parity |

> [!NOTE]
> The English version is canonical. Any normative discrepancy resolves in favour of the English document.

---

## 📖 What is ACS?

The **Agent Context Standard** (ACS) is a lightweight, open format for storing the context an AI coding agent needs to resume work on a project. It complements [Agent Skills](https://agentskills.io): where Agent Skills capture portable *capabilities*, ACS captures portable *project context* — long-lived facts, research, session state, decisions, and specifications.

```
my-project/
├── llm-session/          # ← all agent context lives here
│   ├── MANIFEST.yaml
│   ├── BOOT.md
│   ├── memory/  knowledge/  state/  skills/  specs/  decisions/
└── CLAUDE.md / GEMINI.md / .cursorrules / AGENTS.md
```

`cp -r` that directory and the next agent session resumes with full context.

---

## 🚀 Quick start

The fastest path is the bundled framework skill:

```bash
# From an existing ACS project, copy the framework bundle
cp -r skills.ACS_Framework_v1.0 /path/to/new-project/

# In the new project, run /init in your harness, then say:
"load ACS"   # or  "carga ACS"
```

The bundled `ACS.FRAMEWORK.agent-context-standard` skill detects whether the project already has ACS, creates the canonical layout if not, and reports compliance.

---

## 📁 In this directory

| File / folder | Purpose |
|---|---|
| 📘 [`Agent_Context_Standard_v1.0.en.md`](Agent_Context_Standard_v1.0.en.md) | Full standard (English, canonical, 504 lines) |
| 📕 [`Agent_Context_Standard_v1.0.es.md`](Agent_Context_Standard_v1.0.es.md) | Full standard (Spanish, translation, 504 lines) |
| 📂 [`templates/`](templates/) | Drop-in templates: `MANIFEST.yaml`, `BOOT.md`, `HARNESS.yaml`, `INDEX.md`, harness shims |
| 📜 [`README.md`](README.md) | This index |
| ⚖️ [`LICENSE`](LICENSE) | Full Creative Commons Attribution 4.0 International legal text (verbatim from creativecommons.org) |

For the parent skill that consumes these references, see [`../SKILL.md`](../SKILL.md). For the bundle's read-only invariant and versioning workflow, see [`../../VERSION.md`](../../VERSION.md).

---

## 🛠️ Compatible harnesses

ACS ships shim templates for:

[![Claude Code](https://img.shields.io/badge/Claude%20Code-supported-success.svg)](https://claude.ai/code)
[![Gemini CLI](https://img.shields.io/badge/Gemini%20CLI-supported-success.svg)](https://geminicli.com)
[![Cursor](https://img.shields.io/badge/Cursor-supported-success.svg)](https://cursor.com)
[![Antigravity](https://img.shields.io/badge/Antigravity-supported-success.svg)](https://antigravity.google)
[![Continue.dev](https://img.shields.io/badge/Continue.dev-supported-success.svg)](https://continue.dev)

Any other agent harness that can read files works — create a root shim with the file name your tool auto-discovers and link it to `llm-session/BOOT.md`.

---

## 🧱 Compliance levels

ACS defines five incremental adoption levels:

| Level | Requirements |
|:-:|---|
| **L0** | Canonical directory + `MANIFEST.yaml` |
| **L1** | + `BOOT.md` + all required `INDEX.md` |
| **L2** | + `HARNESS.yaml` + ≥2 entry shims |
| **L3** | + `specs/` with at least one accepted spec |
| **L4** | + `decisions/` with ADRs |

Full details in the [English spec](Agent_Context_Standard_v1.0.en.md#compliance-levels).

---

## 🤝 Contributing

The Agent Context Standard format was originally developed by [CONFIANZA23](https://confianza23.com), released as an open standard, and is being adopted by a growing number of agent products. **The standard is open to contributions from the broader ecosystem.**

Ways to contribute:

* 🐛 **Report issues** — file edge cases, ambiguities, or harness incompatibilities
* 💡 **Propose extensions** — new artifact types, schema fields, or compliance levels
* 🌐 **Translate** — open new `Agent_Context_Standard_v1.0.<lang>.md` files alongside the existing two
* 🛠️ **Build tooling** — verifiers, linters, harness adapters
* 📝 **Improve the spec** — clarify language, add examples, fix typos

Edits to the canonical English document require a corresponding update to translations (line-for-line parity). The [`ACS.docs.bilingual`](../../ACS.docs.bilingual/SKILL.md) skill enforces this.

---

## 📄 License

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

ACS-1.0 is published under [**Creative Commons Attribution 4.0 International (CC-BY-4.0)**](https://creativecommons.org/licenses/by/4.0/) — use it, fork it, adapt it freely, attributing the origin. Full legal text in [`LICENSE`](LICENSE) (verbatim from creativecommons.org).

---

## 🏛️ Maintained by

[**CONFIANZA23**](https://confianza23.com) · Madrid, Spain
Forged during the development of the [STRATOS](https://confianza23.com) platform for Navantia · May 2026.

---

<sub>Agent Context Standard v1.0 · 2026-05-05 · Built on top of [Agent Skills](https://agentskills.io)</sub>
