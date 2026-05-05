---
personal: true
public: true
internal: true
slug: phil-style-prd-cycle
title: PRD Cycle (Draft → Critique → Revise → Spec)
dek: A four-skill discipline for producing high-quality PRDs through iteration — schema-driven drafting, structured critique, version-bumping revision, and gist publication. Each skill consumes the prior step's output via a single source-of-truth schema.
date: 2026-05-05
updated: 2026-05-05
category: recipes
order: 2
stream: internal
---

# PRD Cycle

*A four-skill discipline for producing high-quality PRDs through
iteration — schema-driven drafting, structured critique, version-bumping
revision, and gist publication. Each skill consumes the prior step's
output via a single source-of-truth schema.*

---

## When to use this

You're producing PRDs (or any structured spec document) at a cadence
where the iteration loop is the bottleneck. You've noticed that
unstructured drafts → ad-hoc reviews → vague revisions produce
documents that drift in shape between versions, lose key sections, and
never quite reach "done." You want the same shape every time without
templated boilerplate.

## What it is — four skills, one schema

The schema is the load-bearing piece. It defines:

- The required sections (e.g., Context, Goals, Non-Goals, Architecture,
  Risks, Open Questions, Appendices)
- Per-section heuristics (length, voice, what counts as complete)
- Versioning convention (`v0.{N}` for pre-graduation, `v1.{N}` for
  shipped specs)
- Cross-document references (how this PRD links to others)

The four skills:

### 1. Draft skill

Input: a problem statement + any preliminary research notes.

Output: `v0.1` of a PRD that hits every required section in the schema
with first-pass content. Sections marked `TBD` if information is
missing — explicit gaps are better than fabricated content.

The draft skill's job is **complete-not-perfect**. A `v0.1` with
honest TBDs beats a `v0.1` with confident filler. The next skill in
the cycle will surface the gaps.

### 2. Critique skill

Input: the current PRD version + a `--questions` sidecar that lists
the specific things the reviewer wants probed (cross-comparison
against an earlier version, calibration check against an external
benchmark, output framing for a particular audience).

Output: a structured verdict:

- Top findings — the 3–5 things that most need attention
- Per-section feedback with line-anchored references
- TBD inventory — which `TBD`s in the draft are blockers vs nice-to-haves
- Recommendation — does this version graduate to revise, or send back
  to draft?

The critique skill is the discipline that makes versioning meaningful.
Without structured critique, "v0.2" is just "I edited some things."
With it, every version bump answers a specific set of questions.

### 3. Revise skill

Input: the current version + the critique output + a revision plan
(the human's call on which findings to act on).

Output: `v0.{N+1}` with:

- A revision-plan table at the top (what the new version did differently
  + why) — this is the audit trail for future readers
- Inline resolution of the critique's top findings
- Updated TBDs (more resolved, or honest about remaining unknowns)
- Explicit deviations flagged (sections where the revise skill chose
  not to act on critique feedback, with reasoning)

Revisions don't have to take every critique recommendation. The
deviation-flagging discipline is what keeps the cycle honest — "I
disagreed with finding X for these reasons" is a better outcome than
silently ignoring it.

### 4. Spec-cycle skill (publish)

Input: a graduated PRD version (the human says "this is the version
to publish").

Output:

- A clean copy with version markers normalized
- A public-facing variant if needed (with internal context stripped)
- Publication to a stable URL (gist, internal wiki, document store)
- Cross-reference updates (anything that linked to a draft version
  now points at the published one)

The spec-cycle skill exists because publication is its own discipline.
"Send the PDF in Slack" is not publication — it's distribution.
Publication means a stable URL, a versioned reference, and an audit
trail.

## The schema as single source of truth

Every skill reads the same schema file. When the schema changes (e.g.,
adding a new required section, changing version-numbering convention),
all four skills pick it up automatically.

This is the discipline that prevents skill-drift. Without a shared
schema, the draft skill might add a "Risks" section, the critique
skill might expect a "Risk Mitigation" section, and the revise skill
might rename it to "Open Risks." The schema removes the negotiation
and forces consistency.

## Why this beats one big "write a PRD" skill

A monolithic skill that "writes a PRD" gets long, gets opinionated,
and gets stuck in early-version conventions even when the org's
patterns evolve.

Four small skills that share a schema:

- Each skill is small enough to keep current. The schema is updated
  in one place.
- Versioning is a first-class operation. The skill name itself tells
  you which step you're in.
- Critique and revision become reusable across other document types
  (architecture docs, postmortems, planning briefs). Same shape,
  different schema.
- The audit trail (revision plans, critique outputs, deviations) is
  preserved as a side-effect of the skill flow rather than as
  optional metadata.

## Where this works less well

- **Documents that don't iterate.** A two-paragraph announcement
  doesn't need a four-skill cycle. Use this for documents that go
  through multiple revisions before publication.
- **First-time authors of a document type.** The cycle assumes you
  know what "good" looks like in your schema. If you're starting from
  zero, write a few documents the long way first; let the patterns
  surface; then codify the schema.
- **Synchronous review settings.** The cycle is async-friendly (each
  skill is a discrete invocation). Real-time editing sessions don't
  benefit much.

## Schema-evolution cadence

Schemas decay. Every quarter or so, run a meta-review:

- Which sections never get filled meaningfully? (Drop them.)
- Which sections are always added ad-hoc by the human after the
  draft? (Promote them to required.)
- Which heuristics produced bad outputs? (Revise.)

The schema should evolve with the patterns of the documents you
actually produce. A schema that's been frozen for a year is probably
out of date.

## Related

- [[concepts/phil-style-prd]] — the broader thesis on schema-driven
  PRD discipline
- [[concepts/pm-as-builder]] — the four-skill cycle is one of the
  reusable patterns that makes PM-built tooling worthwhile

## Sources

- Pattern shipped April 2026 across multiple PRD cycles, including
  one that landed engineering-team acceptance on the second revision.

---

*Created: 2026-05-05.*
