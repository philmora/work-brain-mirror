---
personal: true
public: true
internal: true
slug: architectural-lock-discipline
title: When to lock architecture before acting
dek: A reflection on when to route a decision through explicit lock-in versus when to just execute. The case for lock-ins isn't that obvious calls go wrong — it's that the discipline catches the calls where confidence was misplaced.
date: 2026-05-05
updated: 2026-05-05
category: reflections
order: 1
stream: internal
---

# When to lock architecture before acting

*A reflection on when to route a decision through explicit lock-in
versus when to just execute. The case for lock-ins isn't that obvious
calls go wrong — it's that the discipline catches the calls where
confidence was misplaced.*

---

## The tension

In any multi-agent or multi-collaborator system, there's a constant
choice between two disciplines:

1. **Lock before acting.** Write down the decision, route it through
   collaborators, get acknowledgment, then execute.
2. **Act decisively.** When the answer is obvious, just do the thing.
   Ping after if anyone needs to know.

Both are real disciplines. Lock-in produces auditable trails and
catches misplaced confidence; decisiveness produces velocity and
prevents bottlenecking. The wrong call in either direction is
expensive — too much locking and the system grinds to a halt; too
little and you ship something that has to be unwound.

## The case I keep encountering

A multi-agent system: two agents collaborate on a shared surface
(content pipeline, codebase, planning document). One faces a choice
among multiple defensible options — call it Option A vs B vs C. The
agent picks A, executes, and informs the other.

The decision turns out to be right. The work shipped. No unwind needed.

But the *protocol* was bypassed. The lock-in step that exists for
the case where the decision is wrong was skipped on the strength of
"this one's obvious." Every time this happens with a good outcome,
the muscle for lock-ins atrophies. By the time a non-obvious case
arrives, the discipline isn't there to catch it.

## What I think the rule is

The rule isn't "always lock in." That's bottlenecking dressed up as
discipline. The rule is to define **named triggers** — concrete
predicates that, when true, require routing rather than action.

Five triggers I've found useful for the shared-surface case:

1. **Multiple plausible options exist.** If you can defend A and B and
   C, that's a lock-in moment regardless of which one you'd pick.
2. **The action creates a new artifact in shared infrastructure.** A
   new database table, a new route, a new component, a new field.
3. **The action changes a renderer or a transformation layer.** Code
   that other systems depend on.
4. **The action is hard to reverse.** Unwind cost greater than a few
   minutes.
5. **The action affects schema.** Anything that changes the shape of
   data flowing between components.

Outside those triggers, decisive action is the right move. Inside
them, lock-in is the right move regardless of how confident you feel.

## Why named triggers beat "use your judgment"

Two reasons:

- **Judgment under deadline pressure is biased toward decisiveness.**
  The discipline-of-locking-in is exactly the discipline that gets
  abandoned when the build is 80% done. Triggers don't read the room.
- **Self-auditing a checklist of five is tractable. Self-auditing a
  feeling is not.** "Did I face a choice among defensible options?"
  is a question you can answer. "Was that important enough to lock
  in?" is a question that gets answered on the basis of what you
  already wanted to do.

## The framing that works in handoff documents

The right framing for a lock-in is not "I don't know what to do —
help me." It's "Here are the four options, here's my analysis of each,
here's my lean, what do you think?" That format:

- Demonstrates the analysis is complete (not bottlenecking on
  someone else thinking)
- Surfaces the real tradeoffs (not a binary "approve / reject")
- Gives the other side something to push back on (not just nod or
  silence)
- Produces an artifact that documents *why* the chosen option won

A lock-in handoff that doesn't include the lean is just a request for
permission. That's worse than executing — it imports the bottlenecking
without the discipline-of-articulating-options.

## The cost of getting the discipline wrong

Too much locking: every action requires a round-trip; routine work
takes 2x longer; collaborators stop reading handoffs because they're
mostly noise.

Too little locking: occasionally a decision shipped on confidence
turns out to be wrong; the unwind cost is the time of the shipping
plus the time of the unwinding plus the trust cost.

The crossover point depends on how often the "obviously right" call
is actually right. If it's >95% of the time, lighter locking is
correct. If it's <80%, heavier locking is correct. Most cases I've
seen sit somewhere in between, which is why named triggers (rather
than universal rules) end up being the right answer.

## How to recover from a missed lock-in

When a lock-in was bypassed and the decision turned out fine:

1. Acknowledge the protocol drift, not just the outcome. "It worked
   out" doesn't validate the bypass.
2. Update the trigger list if needed. Maybe this case revealed a
   trigger that wasn't in the list, or revealed a trigger that's too
   restrictive.
3. Don't punish the bypass. The framing should be "next time this
   shape appears, route it" — not "you violated the rule."

When a lock-in was bypassed and the decision turned out wrong:

1. Unwind the action.
2. Treat the unwind as the protocol's tax — write it up, share it
   across the team, make it the next training case for why the
   discipline exists.
3. Same trigger-list update, more weight.

## Where this generalizes

- Code review (lock-in = "post the design doc; act = "merge directly")
- Planning meetings (lock-in = "write the proposal in advance"; act
  = "decide in the meeting")
- Personal habit changes (lock-in = "write the rule down and share
  it"; act = "just start doing it")
- Multi-agent automation (the case this reflection emerged from)

The trigger list is domain-specific, but the meta-pattern (named
triggers > universal rule > judgment) seems portable.

## Related

- [[concepts/two-claude-protocol]] — the multi-agent coordination
  pattern this reflection emerged from
- [[recipes/airlock-publishing-pipeline]] — the system whose shared
  surface the rev5 incident affected

## Sources

- A specific incident on a shared publishing pipeline, May 2026.
  Outcome: shipped successfully; protocol drift logged; recalibration
  delivered as a rev6 handoff with named triggers added to the
  context briefing.

---

*Created: 2026-05-05.*
