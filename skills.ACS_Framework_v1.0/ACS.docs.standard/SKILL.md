---
name: ACS.docs.standard
description: Use when the user asks to design / write / publish a public-facing standard (open spec, RFC, convention) that others can adopt independently — distinct from internal SPEC.md (which is a project contract). Provides the proven section anatomy: motivation → 60-second TL;DR → design principles → layout/schemas → reference implementation → FAQ → comparison with alternatives → roadmap → license. Triggers on phrases like "diseña un estándar", "design a standard", "open spec", "convention for X", "make this adoptable".
license: CC-BY-4.0
metadata:
  version: "1"
  validation: "Used 2026-05-05 to package ACS-1.0 (Agent Context Standard) into Agent_Context_Standard_v1.0.es.md/Agent_Context_Standard_v1.0.en.md, complementing the formal SPEC.md in specs/ACS-001-*. Sections proven: 21 in each language, 615 lines, every required section present."
---

# Authoring an open standard for public adoption

A SPEC.md is the **internal contract** for a piece of work. A public **standard** is something completely different: a document that strangers will fork, adopt, and build tooling around. It needs marketing-quality clarity, defensive answers to obvious questions, and a forking-friendly license.

This skill captures the section anatomy that worked for ACS-1.0 and generalises it.

## When to invoke

- User says "diseña un estándar", "design a standard", "make this an open spec", "publish a convention".
- A pattern that started as project-internal needs to become public so other teams adopt it.
- Existing SPEC.md is too contractual / dry for outsiders to engage with — needs a public companion.

## Required section anatomy (in order)

This 13-section structure is proven token-efficient for both authors and adopters. **Do not reorder.** Skip a section only if it genuinely doesn't apply (and document why).

| # | Section | Purpose | Length |
|---|---|---|---|
| 1 | **Title + tagline** | One-line definition that makes scope obvious to a stranger. | 1 line |
| 2 | **Cross-language links** | If bilingual: language banner + link. If single-language: skip. | 1–2 lines |
| 3 | **Table of contents** | Anchor-linked. Mandatory for >300-line docs. | 20–30 lines |
| 4 | **Why X (motivation)** | The problem this solves. State the pain by enumerating current alternatives' shortcomings. | 100–200 words |
| 5 | **In 60 seconds (TL;DR)** | A single code block / diagram + 2 short paragraphs that explain the essence to a hurried reader. If they only read this section, they should know if the standard is for them. | 150–250 words |
| 6 | **Design principles** | 4–8 numbered, opinionated, binding principles that drove the design choices. Each principle is a single sentence + one paragraph of rationale. | 1 paragraph per principle |
| 7 | **Standard layout / schema** | The actual normative content: directory layout, file formats, frontmatter schemas, table conventions. Use code blocks generously. | bulk of the doc |
| 8 | **Naming conventions** | Filename patterns, ID prefixes, casing rules. Tabulate. | 30–60 lines |
| 9 | **Reference templates** | Copy-pasteable templates for every required artifact. | 100–200 lines |
| 10 | **Compliance levels** | L0 → L1 → L2 → ... gradient so adopters can ship partial conformance and progress. Avoid binary "compliant/not compliant" — too brittle. | 10–20 lines |
| 11 | **Migration / adoption guide** | How to adopt from scratch (Case A) and how to migrate from a non-standard layout (Case B). Include shell commands. | 50–100 lines |
| 12 | **Conformance verifier** | A bash one-liner or script that an adopter can run locally to check their compliance. Critical for trust. | 20–40 lines |
| 13 | **FAQ** | 6–12 questions the standard's author already knows will be asked. Pre-empt the obvious objections. | 200–400 words |
| 14 | **Comparison with alternatives** | Capability matrix: alternatives in columns, capabilities in rows, ✓/partial/✗. Honest about where alternatives win. | one table |
| 15 | **Roadmap** | v1.1, v1.2, v2.0 vision. Signals the standard is alive without overcommitting. | 30–60 lines |
| 16 | **License & acknowledgements** | License declaration (CC-BY-4.0 is a sensible default for standards); inspirations and prior art credited. | 20–40 lines |

## Tone and voice rules

1. **Use active voice and "MUST / SHOULD / MAY"** (RFC 2119) for normative requirements: *"a compliant harness MUST consult HARNESS.yaml"*. Reserve plain prose for guidance and rationale.
2. **State the why before the what.** A principle without rationale will be ignored. Rationale lives in the body, not in comments inside code blocks.
3. **Be honest about cons.** A standard that hides its trade-offs loses credibility. The Comparison section MUST show capabilities where alternatives win.
4. **Quote the user when capturing motivation.** A direct user quote in the "Why" section grounds the standard in real pain. Anonymise if needed.
5. **Avoid jargon walls.** Define every acronym at first use, even obvious ones (yes, even "JSON"). Adopters come from many backgrounds.
6. **Prefer tables over prose** for any 3+ items with 2+ attributes each. The token cost is similar; the scan cost for the reader is much lower.

## Pairing with the formal SPEC.md

A public standard typically has TWO documents:

- **`<NAME>_README.md`** (this skill) — public-facing, accessible, marketing-grade, bilingual if needed. Lives at project root.
- **`specs/<ID>-<slug>/SPEC.md`** — formal contract, terse, normative, immutable-once-accepted. Lives inside ACS-compliant `llm-session/specs/`.

The README links to the SPEC for the formal contract; the SPEC may link back to the README for context. They co-evolve but serve different audiences.

## Anti-patterns

- **Specs as marketing**: a SPEC.md should not have a "Why" section, a roadmap, or a comparison table — that bloats the contract and dilutes the normative content. Keep them separate.
- **Marketing without normative substance**: a README without templates and a verifier is just a pitch deck. Adopters need to be able to ship.
- **Versioning the README without versioning the spec**: the SPEC owns the version number. The README references it.
- **Skipping the FAQ**: every standard's first 6 months are spent answering the same questions. Pre-empt them.
- **Roadmap with promises**: "v1.1 will ship in Q3" is a hostage. Use "planned" / "vision" verbs.
- **License omission**: an unlicensed standard cannot be adopted by serious organisations. CC-BY-4.0 is the safe default; consult counsel for stricter regimes.

## Validation

Authored 2026-05-05 to package the Agent Context Standard (ACS-1.0). The resulting `Agent_Context_Standard_v1.0.es.md` (ES) and `Agent_Context_Standard_v1.0.en.md` (EN) include all 16 sections in the table above (615 lines each). The companion `specs/ACS-001-agent-context-standard/SPEC.md` carries the formal normative content (300+ lines, immutable). Pairing has held for the lifetime of the standard so far.

Combine with [`skills.ACS.docs.bilingual`](../ACS.docs.bilingual/SKILL.md) when the standard ships in multiple languages.
