---
personal: true
public: true
slug: two-claude-protocol
title: Two-Claude Protocol
dek: A coordination pattern for work that spans two laptop environments — two LLM agents that don't talk directly, with a human router moving handoff documents between them. Validated by one full ship cycle.
date: 2026-04-30
updated: 2026-05-04
category: concepts
order: 7
---

# Two-Claude Protocol

*A coordination pattern for work that spans two laptop environments — two LLM
agents that don't talk directly, with a human router moving handoff documents
between them. Validated by one full ship cycle.*

---

## What it is

Two LLM agents operating on two separate laptops, coordinating through a human
router. The agents never talk to each other directly — handoff documents are
moved between machines by hand (AirDrop, file transfer, whatever moves bytes).
Each agent operates in its own filesystem with its own tools and credentials;
neither has visibility into the other's working state except through what the
human routes.

The pattern emerged from a real build: one laptop owned the source of truth
(a personal wiki and an airlocked publishing pipeline) and the other owned the
rendering layer (the public site). It scales to any work that requires both
contexts — different filesystems, different installed tools, different
credentials, different privacy boundaries.

## Why it matters

Two laptops are not interchangeable. Work and personal contexts each have
constraints the other doesn't: organization-managed devices have monitoring
and credential restrictions; personal devices have the publishing
infrastructure but lack work-system access. Trying to do everything from one
laptop forces tradeoffs — work content on a personal machine creates an
unclear privacy story, personal publishing infrastructure on a managed
machine bleeds credentials and adds friction.

The protocol lets each agent stay in its lane while the work moves forward.
The human's role as router is light — paste a doc, walk to the other laptop,
paste a reply — but the routing is the load-bearing piece. Without a clear
handoff format, both agents end up confused about state and overlapping on
each other's work.

## Key disciplines

- **Single source of truth per handoff thread.** The doc gets revved
  (rev 1 → 2 → 3 → 4), not re-authored each turn. Each rev keeps the prior
  content and appends. Easier to audit, easier to spot what changed, honest
  about the trail. Don't squash; don't rewrite the handoff history.

- **Architecture locks before implementation.** Fork-vs-extend, design
  tokens, schema decisions get nailed down in a handoff exchange before
  either agent writes code. Saves rework. In practice, ~5 architectural
  calls were locked in one rev, the next rev was acknowledgement, the next
  was closing-the-loop. Implementation work happens between locks, not
  inside them.

- **Backup before risky work.** Whichever agent is about to touch shared
  production state takes a backup commit first. Standard hygiene; it
  matters when something goes wrong, and "something goes wrong" is the
  default state of any non-trivial build.

- **Flag speculative deliverables explicitly.** When one agent produces a
  visual or structural artifact for a system it can't directly see, the
  artifact must be labeled "speculative — verify against live values." On
  the build that surfaced this protocol, the first visual prototype used
  guessed design tokens and had to be rebuilt after the other agent
  inspected the live site values. Either ask for the tokens before
  producing the prototype, or label the output speculative.

- **The human routes; the human doesn't translate.** Documents move
  verbatim between laptops. The router doesn't rephrase, summarize, or
  condense — that adds a third interpretation layer between two agents who
  already share enough context vocabulary to understand each other directly.
  If something is unclear, it goes back as a question, not as a gloss.

- **Maintain an open coordination thread.** Each handoff has a checklist of
  open items at the bottom. Each rev updates the boxes — done, pending,
  ownership noted per item. This is the single shared status surface. It's
  how both agents know they're working on the same plan.

## Failure modes to watch

- **Stale handoff knowledge.** If too long passes between handoffs, an
  agent's view of "current state" drifts. Mitigation: every handoff begins
  with a state-verification step (read the actual repo, the actual file,
  the actual production URL) before acting on the doc.

- **Skipping architecture locks under deadline pressure.** Tempting when
  the build is 80% done. Cost: the last 20% includes reversed decisions,
  wasted implementation, and re-litigation in handoff revs. Footguns
  catalogued late are footguns that cost full iteration cycles.

- **Router-as-bottleneck for trivial routing.** If every body change
  requires a round-trip, the protocol slows the work. Mitigation: design
  protocols where most operations are unilateral (e.g., body changes need
  no second-agent step — runtime fetch picks them up) and only schema or
  structural changes route through both agents.

- **Privacy ambiguity at the boundary.** Anything that crosses the work /
  personal boundary is a privacy decision. The mechanism that enforces it
  (an airlock script, a forbidden-term glossary, a manual review gate)
  needs to be explicit, not implicit. Fuzziness here is the most expensive
  bug to find.

## Why this beats single-laptop alternatives

Running both agents on one machine is simpler. It's also the wrong answer in
two real cases: when the work crosses a privacy boundary the machine doesn't
respect, and when the toolchain is split (e.g., publishing infrastructure
lives on the personal machine, knowledge sources live on the work machine).

Running one agent that hops between machines is also possible. It loses the
parallelism — both agents working in parallel on different layers of the
build is the productivity story. A single shared agent serializes.

## Related

- [[decisions/work-brain-mirror-public]] — a privacy boundary clarified across the protocol

## Sources

- Personal thesis. Validated by one full ship cycle of a public publishing
  pipeline, April 2026.

---

*Created: 2026-04-30. Last updated: 2026-05-04.*
