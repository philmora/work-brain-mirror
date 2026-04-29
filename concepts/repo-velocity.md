---
personal: true
public: true
slug: repo-velocity
title: Repo Velocity
dek: Measuring engineering output in the AI era by combining quantitative signals with LLM scoring — to separate substantive work from operational noise.
date: 2026-04-22
updated: 2026-04-22
category: concepts
order: 1
---

# Repo Velocity

*Measuring engineering output in the AI era by combining quantitative signals with LLM scoring — to separate substantive work from operational noise.*

---

## What it is

Repo velocity is a composite measure of engineering output that pairs classic quantitative signals (throughput, cycle time, review latency, PR size, AI adoption) with a qualitative layer: a capable LLM scoring every merged PR on complexity, impact, and craft. The two layers answer different questions. Quantitative tells you *how much* shipped. The LLM layer tells you *what actually shipped*.

The central insight: counting PRs in 2026 is like counting keystrokes in 2010. Volume is cheap. What matters is the fraction of work that moves the system — Substantive Repo Activity Units, SRAU — versus operational noise like release bumps, cherry-picks, and AI-generated formatting passes.

## Why it matters

Every existing tool — GitHub Insights, Jira, sprint velocity, DORA — counts. None assess. A repo can ship 69 PRs in a week with healthy median cycle time and look strong on DORA, while two-thirds of that throughput is release hygiene. A team can sit at 62% AI adoption and produce less substantive work than a team at 31%. Quantitative tooling cannot tell you which of those things is true.

The LLM layer closes that gap cheaply. A calibrated LLM pass over every PR at roughly $0.002 per PR behaves like a tireless principal engineer reading every diff. That economic shift — expert judgment at ingredient-level cost — is what makes measurement beyond volume newly tractable.

This is not a productivity-tracking tool. It is a diagnostic: where is the team bottlenecked, where is the work shallow, where is AI doing routine work well, and where is AI volume masking a lack of hard problems.

## Key points

- Two layers, not one. Quantitative metrics counterbalance LLM judgment; LLM judgment contextualizes quantitative metrics. Neither alone produces the signal.
- SRAU ratio is the central metric. Of the PRs that merged, what fraction did meaningful work? First-pass results on five mature engineering repositories landed between 26% and 52%.
- AI adoption does not correlate with SRAU. In the first experiment, the highest-AI-adoption repo (62%) had the second-lowest SRAU. AI shipping volume is not the same as AI shipping value.
- The design resists gaming. Seven weighted dimensions counterbalance — throughput pulls against complexity, speed pulls against quality, AI adoption pulls against SRAU. Optimizing any single dimension degrades the composite.
- The unit of measurement is the codebase, not the developer. This sidesteps the perverse incentives of individual productivity tracking — agent-era measurement is a property of the repository, not the person.

## Illustrative output

First-pass run of the tool over a one-week window on five anonymized production repos:

| Repo | PRs | RVI | SRAU% | AI% | Top Signal |
|------|-----|-----|-------|-----|------------|
| A | 69 | 74 | 36% | 52% | Low complexity — under-investing in hard problems |
| B | 65 | 75 | 52% | 31% | Strong throughput + highest SRAU ratio |
| C | 13 | 70 | 31% | 62% | Highest AI adoption, but agents on routine work |
| D | 23 | 56 | 26% | 52% | 51h median merge time — review bottleneck |
| E | 0 | — | — | — | No PRs in the window |

Five things that fall out once you run the two-layer measurement:

1. A large fraction of Repo A's throughput is operational noise — release bumps, cherry-picks. Dashboards that count PRs read this as elite velocity.
2. Repo D has a review bottleneck worth roughly 20 RVI points. Quantitative tooling shows the 51h median but doesn't weight it against the substance being reviewed.
3. Repo B ships higher-impact work at comparable PR volume to Repo A. Without LLM scoring the two look identical on DORA.
4. AI adoption (31–62% across the set) does not correlate with SRAU ratio. Volume of AI-assisted PRs is not evidence of AI-assisted value.
5. Average complexity across all five repos sits at 4.2/10. The shallow-work signal is not specific to one team.

## Caveats

- Short windows favor noise. Longer windows weight the evidence better.
- LLM scoring uses a single model with a calibration pass. Scores are internally consistent within a run but not directly comparable across runs of different prompts or model versions.
- The health dimension — agent-readiness scoring — is harder to operationalize and was a placeholder in the first pass.

## Related

- [[concepts/agentic-readiness]] — the health dimension draws from agentic-readiness signals
- [[concepts/context-engineering]] — the LLM scoring layer is context engineering applied to measurement
- [[concepts/pm-as-builder]] — a PM-built measurement tool is itself an instance of the builder thesis

## Sources

- Personal thesis + first-pass experiment, April 2026. Tool was a one-week build.

---

*Created: 2026-04-22. Last updated: 2026-04-22.*
