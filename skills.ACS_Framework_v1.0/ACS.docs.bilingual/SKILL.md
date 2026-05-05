---
name: ACS.docs.bilingual
description: Use when the user asks for documentation in two (or more) languages and expects the versions to stay in lockstep — typical for READMEs, public standards, marketing pages, internal handbooks for multilingual teams. Enforces structural parity (identical headings, anchors, tables, code blocks, line count) so future edits do not desync. Triggers on phrases like "in Spanish and English", "ambos en castellano y en inglés", "bilingual readme".
license: CC-BY-4.0
metadata:
  version: "1"
  validation: "Used 2026-05-05 to author README.md/README.en.md (797/797 lines paired) and Agent_Context_Standard_v1.0.es.md/Agent_Context_Standard_v1.0.en.md (615/615 lines paired)."
---

# Bilingual documentation with structural parity

## When to invoke

- User asks for a doc "in Spanish and English" / "ambos en castellano y en inglés".
- A new README, standard, or marketing page is being authored and a translation is part of the deliverable.
- An existing doc gains a translation request and you need a procedure that prevents drift over time.

## Filename convention

Default to a primary language and an `.<lang>.md` suffix for the secondary:

```
README.md          ← primary (often source language)
README.en.md       ← English secondary
README.fr.md       ← optional French
Agent_Context_Standard_v1.0.es.md      ← primary (Spanish)
Agent_Context_Standard_v1.0.en.md   ← English
```

The primary file does NOT carry a language suffix (avoids needing to update existing links). Secondary files declare their language in the suffix and at the top with a 🇬🇧 / 🇪🇸 emoji + cross-link.

## Procedure

1. **Write the source language fully first.** Don't half-translate; write the whole primary doc, settle on its structure, and ship it. Translation drift is harder to fix than slow drafting.
2. **Lock the structure** — every heading, table, code block, list, and anchor link in the source becomes a non-negotiable structural element of the translation. Do not reorder, split, or merge sections during translation.
3. **Translate top-to-bottom in one pass.** Keep the editor showing source on the left, target on the right. Translate paragraph by paragraph; do not skip sections.
4. **Identical structural elements:**
   - Same section count and same H2 / H3 hierarchy.
   - Same number of table rows and columns; only cell text translated, headings translated, structure identical.
   - Code blocks copied verbatim; comments inside code optionally translated.
   - Links: external URLs identical; internal anchors regenerated to match the translated heading slug (Markdown engines lowercase + replace spaces with dashes).
5. **Cross-link headers** at the top of each file:
   ```markdown
   > 🇪🇸 Documento en castellano. English version: [README.en.md](README.en.md).
   > 🇬🇧 English document. Spanish version: [README.md](README.md).
   ```
6. **Verify line-count parity** with a one-shot bash check:
   ```bash
   wc -l README.md README.en.md
   ```
   A delta of more than ~3% (e.g. 100 vs 110 lines) suggests a missing section. Investigate before shipping.
7. **Tag both files in `MANIFEST.yaml.project.languages`** (or equivalent in your convention) so future tooling knows the doc is multilingual.

## Translation conventions (ES ↔ EN)

| Source pattern | Target language convention |
|---|---|
| Spanish article + noun | English bare noun (no article) where idiomatic |
| Spanish "que" subordinate clause | English participle / shorter clause |
| Tables: keep cell width visually similar | acceptable to break into 2 lines via `<br>` if needed |
| Number formatting `1.234,56` (ES) | `1,234.56` (EN) |
| Currency `€1.250` (ES) | `€1,250` (EN) |
| Date `5 de mayo de 2026` (ES) | `5 May 2026` or `May 5, 2026` (EN) |
| Decimal point `12,895` (ES) | `12.895` (EN) — be careful with technical figures! |

**Number-formatting traps**: `12,895 USD/t` in Spanish means twelve-thousand-eight-hundred; in English it could mean twelve-point-eight-nine-five. When in doubt, use thousands separator that's unambiguous (`12 895 USD/t` with non-breaking space) or specify "twelve thousand eight hundred ninety-five".

## Anti-patterns

- **Half-translating then publishing**. Better to ship the source-only version than a half-translated one.
- **Different section orders between languages**. Breaks anchor links and creates real maintenance debt.
- **Auto-translating with a model and not reading the output**. The first sweep must be human-reviewed for technical-domain terminology.
- **Letting one language drift over time** — document the parity rule in CONTRIBUTING.md or memory.

## Maintenance over time

When the source doc changes:

1. Apply the change to the source file first.
2. Immediately mirror the change to every translation. Do not defer.
3. If you cannot translate immediately, add a banner at the top of the stale translation: `> ⚠ Out-of-date — see [primary] for latest. Last sync: YYYY-MM-DD.`
4. Schedule the catch-up; do not let staleness become permanent.

## Validation

Authored 2026-05-05 to package the bilingual deliverables for STRATOS:
- `README.md` (ES) ↔ `README.en.md` (EN): 797 / 797 lines, 5 832 / 5 394 words, 20 sections each, identical structure including 5 evidence-source tables and 7-row COA strategy table.
- `Agent_Context_Standard_v1.0.es.md` (ES) ↔ `Agent_Context_Standard_v1.0.en.md` (EN): 615 / 615 lines, 3 322 / 3 095 words, 21 sections each, identical structure including FAQ block (9 Q&As) and comparison table (5 columns × 11 rows).

Verified line-count parity with `wc -l` post-write; no section drift.
