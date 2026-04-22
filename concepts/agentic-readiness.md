---
personal: true
public: true
---

# Agentic Readiness

*The principle that autonomous agent capability is a property of the repository,
not the developer — prepare the codebase, not the person.*

---

## What it is

Agentic readiness is the idea that whether an AI agent can do useful work in
a codebase depends almost entirely on the codebase itself — its documentation,
test coverage, context files, and guardrails — not on the skill of the person
prompting the agent. A well-prepared repo lets any developer (or agent)
produce quality work. A poorly prepared repo defeats even the best agent
within minutes.

This inverts the common framing around "AI upskilling." Most organizations
focus on training people to use AI tools. Agentic readiness says: fix the
repo, and every developer and every agent benefits equally. Upskilling
individuals doesn't scale. Repository preparation does.

## Why it matters

Agents fail within minutes in most real-world repositories today. The gap
isn't model capability — it's repo preparedness: missing docs, insufficient
tests, no AI-specific context files, no safety model for autonomous operation.

The compound effect is what makes this strategic. Every hour invested in repo
readiness pays dividends across every developer and every agent that touches
that repo, forever. It's a one-time cost with permanent returns — the opposite
of training investments, which decay the moment someone changes jobs or the
tooling changes.

## Key points

- Agents need the same things good onboarding needs: clear docs, working
  tests, architectural context, defined boundaries
- The measurement is autonomous runtime — how long an agent works before
  requiring human intervention. Today: minutes. Target: hours to days.
- Safety guardrails are non-negotiable: scoped permissions, human code review
  before merge, monitoring, audit trails, rollback plans
- A per-repo champion model works in practice: one IC owner, a few hours a
  week, running agent tests and documenting failures
- AI context files (`.cursorrules`, `AGENTS.md`, `CLAUDE.md`) are the repo's
  instruction manual for agents — treat them as first-class code artifacts

## Sources

- Personal thesis, refined through hands-on work on an in-progress
  repo-AI-enablement program

---

*Created: 2026-04-14. Last updated: 2026-04-21.*
