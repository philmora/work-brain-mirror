---
personal: true
public: true
---

# Context Engineering

*The discipline of filling the LLM context window with just the right
information for the next step — not too much, not too little, structured
deliberately.*

---

## What it is

Context engineering is what prompt engineering grows up to become. Where
prompt engineering focuses on crafting a single instruction, context
engineering manages the entire information environment an LLM operates in:
task descriptions, few-shot examples, retrieved content, tool definitions,
conversation state, and compacted history. The goal is always the same —
find the smallest set of high-signal tokens that maximizes the likelihood
of the desired outcome.

Karpathy's framing makes it intuitive: the LLM is a CPU, the context window
is RAM, the filesystem is disk, and agents are long-running applications.
You don't load everything into RAM just in case. You page in what the current
operation needs, operate on it, and page it back out. Every additional token
in context degrades recall on every other token — this is measurable (context
rot), and it means pre-loading "just in case" actively hurts performance.

## Why it matters

This is the foundational concept behind this entire wiki. The Karpathy LLM
Wiki pattern is context engineering applied to personal knowledge management:
structured markdown files on disk (the "filesystem"), loaded into the context
window (RAM) just-in-time based on what the current query needs, navigated
via an index file that the LLM reads to decide which files to load.

It's also the operating principle behind day-to-day agent work: the top-level
instruction file stays small and high-signal, custom skills load just-in-time
context, subagents isolate context-heavy research so they don't pollute the
parent, and a shared-memory system persists facts across sessions so they
don't need to be re-derived.

## Key points

- Anthropic's definition: "the set of strategies for curating and maintaining
  the optimal set of tokens during LLM inference"
- Context rot is real and measurable — the 10,000th token is less trustworthy
  than the 10th
- Always-load sets should be small (~2–4K tokens). Everything else is JIT.
- Subagents are context isolation tools — dispatch research to a subagent,
  get back a summary, keep the parent context clean
- The radical Karpathy move: "No RAG — the LLM reads its own index."
  File-level retrieval, not chunk-level.
- "RAG retrieves and forgets. A wiki accumulates and compounds." — this is
  why the wiki pattern beats traditional "chat with your docs" products

## Related

- [[concepts/agentic-readiness]] — context engineering applied to codebases
- [[concepts/pm-as-builder]] — the thesis that motivates practicing context engineering daily
- [[concepts/repo-velocity]] — the LLM scorer is context engineering applied to measurement

## Sources

- Karpathy's LLM Wiki gist — public gist on GitHub under his profile
- Karpathy on X, June 2025 — post about the LLM Wiki pattern
- Anthropic: "Effective context engineering for AI agents" (September 2025)

---

*Created: 2026-04-14. Last updated: 2026-04-21.*
