---
personal: true
public: true
---

# Vibe-Driven Product Development

*PMs and domain experts build functional prototypes that validate ideas with
real data before engineering commits capacity — separating prototyping from
production.*

---

## What it is

Vibe-driven product development is the practice of having product managers
and domain experts build working prototypes themselves — not mockups, not
specs, but functional software that validates an idea against real data.
The key separation: prototyping is fully decoupled from production
engineering. Dev teams receive battle-tested requirements and real datasets
instead of spec documents.

The enabling layers are concrete, and without them this is aspiration, not
workflow:

1. **Rapid scaffolding templates** — shared starter kits with the org's
   design system, auth, and data access already wired, so a non-engineer
   can stand up a working app in minutes
2. **Safe deployment environments** — isolated dev and prod-adjacent targets
   for non-engineer work, with guardrails that keep experiments from
   touching real production
3. **A clean handoff model** — a defined pattern for taking validated
   prototypes into the production engineering pipeline without a full
   rewrite

With those three, "PMs should build" stops being an aspiration and becomes
a workflow.

## Why it matters

The biggest source of wasted engineering cycles is building features that
don't survive contact with real usage. Specs are ambiguous by nature — a
working prototype eliminates that ambiguity. When a PM can build a prototype,
validate it with users, iterate on it, and hand engineering a proven concept
with real data patterns, the engineering team starts from certainty instead
of assumption.

This also changes who can meaningfully contribute to product delivery. The
historical bottleneck — "only engineers can build" — dissolves when the
platform provides tools that let technical-but-not-engineer contributors
produce real software.

## Key points

- The exit criterion for an org serious about this is simple: enabling
  layers shipped *and* at least one prototype built by a non-engineer
  end-to-end on the platform. Without a proof point, the layers stay on
  paper.
- This only works if the underlying platform is agentic-ready — agents and
  non-engineer humans need the same well-structured environment to be
  productive
- Honest assessment: PM prototyping is an active build target in most orgs,
  not proven capability. Early proof points confirm the model; broader
  adoption is a multi-quarter effort.
- The naming matters. "Vibe coding" is playful and describes the *feel*
  (exploratory, fast, low-ceremony), but the discipline underneath is
  serious — real validation, real data, real handoffs.

## Sources

- Personal operating thesis, informed by an in-progress platform build
  supporting PM and domain-expert prototyping

---

*Created: 2026-04-14. Last updated: 2026-04-21.*
