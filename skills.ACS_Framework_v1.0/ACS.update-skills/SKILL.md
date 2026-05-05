---
name: ACS.update-skills
description: Trigger when the user types "actualiza skills" (or "update skills"). Re-audits the project `skills/` directory ONLY: replace any SKILL.md whose body has been superseded by a faster or cheaper variant learned in the current/recent sessions, add new validated skills, mark superseded ones, and remove broken ones. Maintains the project `skills/INDEX.md`. **NEVER modifies anything inside `skills.ACS_Framework_v<X>.<Y>/`** — that bundle is read-only; framework changes go through the draft → promote workflow described in the bundle's VERSION.md. ACS-aware: also updates `specs/STATUS.md` if a skill change implements a spec, and writes a one-line entry into the most recent `state/session_*.md` snapshot.
license: CC-BY-4.0
metadata:
  version: "2"
  validation: "Used 2026-05-05 to add sdd-spec-authoring + adr-authoring; supersede local-first-state with redirect to agent-context-standard; refresh INDEX.md."
---

# Update skills (actualiza skills)

Re-audits the **project** `skills/` directory ONLY. Use this when the user types `actualiza skills` (or `update skills`).

## Scope (binding)

This skill operates on the project's evolving `skills/` directory. It **MUST NOT** modify any file inside `skills.ACS_Framework_v<X>.<Y>/` — that bundle is read-only and versioned. If during the audit you detect that a framework skill needs change:

1. Stop the in-place edit.
2. Report to the user the proposed change.
3. Recommend the draft workflow: `cp -r skills.ACS_Framework_v1.0 skills.ACS_Framework_v1.draftN` (next available draft slot), then iterate inside the draft, then manual verification + promotion to next stable version.
4. Do not touch the read-only bundle until the user explicitly initiates and completes the promotion.

See `skills.ACS_Framework_v<X>.<Y>/VERSION.md` for the full draft → promote workflow.

## Procedure

1. **Inventory** — read `skills/INDEX.md` and every `<name>/SKILL.md`'s frontmatter (name + description + version + status if present). Cheap; do this with one Glob + Read per file's first 10 lines. **Do NOT inventory the framework bundle** — it is out of scope for this skill.
2. **Per-skill audit**:
   - **Superseded?** Has a more general / faster / cheaper variant been authored this session? If so, mark the old as `status: superseded` and add `superseded_by: <new-name>` to its frontmatter; rewrite its body to a one-paragraph redirect.
   - **Stale steps?** Have file paths, library versions, or APIs changed since? If so, fix and bump `version`.
   - **Still validated?** If recent use confirmed it works, refresh `validation:` with a one-line note + date.
   - **Obsolete?** If the technique no longer applies (e.g. a feature was removed), `rm -rf` the dir and remove from INDEX.
3. **New skills** — for each technique used and validated this session that isn't yet captured:
   - `mkdir -p skills/<kebab-name>` and write `SKILL.md` from the standard template (frontmatter + Procedure + Anti-patterns + Validation).
   - Validation gate: only save if used in-session AND user did not push back. Pushback → memory/feedback entry instead.
4. **Refresh `INDEX.md`** — one line per skill, frontmatter description verbatim. Cap 200 lines.
5. **ACS hooks** (when ACS is in use):
   - If a skill change implements an accepted spec, update that spec's `STATUS.md` table.
   - If a skill change is a new architectural choice, write an ADR (use `skills.ACS.adr` skill).
   - Write a one-line entry into the most recent `state/session_<DATE>.md` under "Skills updated".
6. **Report to the user** in chat — table with added / updated / superseded / removed and a one-line rationale per change. Mention the token-saving impact when relevant.

## Format rules (Anthropic Agent Skills + ACS extensions)

- One folder per skill, lowercase kebab-case name matching frontmatter `name`.
- Frontmatter required: `name`, `description`. ACS-extended: `version`, `status` (active/superseded/deprecated), `validation`, `superseded_by`.
- Description is **model-readable**: state *when* to invoke in concrete triggers ("Use when…", "Triggers on phrases like…").
- Body in markdown. Lead with the goal in one sentence, then steps, then anti-patterns, then validation note.
- Reference sister skills by name (e.g. "see `app.scrap.price.cuni`"); reference specs by ID; reference ADRs by ID.
- **Cap 250 lines per body**. Split if larger.

## What counts as "validated"

A technique is validated iff:
1. **Used in this session** end-to-end (not just discussed).
2. **User did not push back** after seeing the result.
3. **Reusable** — the technique is general enough to apply outside the originating context.

If 1 + 2 but not 3 → still save, but note the scope explicitly in `description`.

If the user pushed back → capture the correction as a `feedback` entry in `llm-session/memory/`, not as a skill.

## Token-saving criteria for updates

A faster/cheaper variant deserves an update when it meets at least one of:

- Reduces tool calls per application (one Edit replaces N reads-then-edits).
- Eliminates an install step (built-in module replaces npm install).
- Single-pass replaces multi-pass refactoring.
- Avoids re-reading a large file by capturing the relevant offset directly.
- Replaces a 3-step manual procedure with a one-shot script.

Always state the saving in the changelog: `v2 (2026-05-05): switched from N edits to 1 replace_all; saves ~3 tool calls per use.`

## Anti-patterns

- **Renaming a skill in place** — breaks any external reference. Supersede instead.
- **Editing a skill body without bumping `version`** — defeats the changelog.
- **Adding a skill that duplicates an existing one** — extend the existing one or supersede it.
- **Validation note copied from a previous skill** — be specific about WHAT was validated and WHEN.

## Validation

Defines its own update protocol; bumped to v2 on 2026-05-05 with ACS hooks (specs/STATUS.md, decisions/, state snapshot tie-in) and explicit anti-patterns.

## Changelog

- **v2 (2026-05-05):** added ACS hooks (update specs/STATUS.md and state snapshot when relevant); explicit validation gate (used + accepted + reusable); anti-patterns section; per-update token-saving annotation requirement.
- **v1 (2026-05-04):** original audit + index refresh procedure.
