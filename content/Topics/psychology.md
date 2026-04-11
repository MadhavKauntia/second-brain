---
title: Psychology
type: topic
created: 2026-04-11
updated: 2026-04-11
notes: []
related: [Concepts/dopamine, Concepts/motivation-maintenance, Topics/distributed-systems]
---

# Psychology

## What this topic covers

The science of mind and behavior — motivation, cognition, emotion, and the biological substrates underlying them. Currently anchored in neuroscience: the mechanisms behind motivation, reward, and behavior change.

## Core concepts

- [[Concepts/dopamine]] — the neuroscience of dopamine: baseline/peak mechanics, the ratio principle, the readily-releasable pool, cold water's unique profile, caffeine's receptor mechanism
- [[Concepts/motivation-maintenance]] — sustaining long-term motivation: intermittent reinforcement, reward stacking avoidance, accessing process rewards, burnout as baseline suppression

## Key sources

- [[Sources/dopamine]] — Huberman Lab lecture on dopamine's role in motivation, focus, and satisfaction; the peak-to-baseline ratio as the core framework

## Synthesis

The first Psychology source introduces a theme that resonates across the wiki: **relative state matters more than absolute state**. Dopamine experience is determined by the peak-to-baseline ratio, not the peak level itself. The same principle appears in distributed systems: DDIA shows that what matters for consistency is the gap between expected and actual data freshness, not the absolute freshness (see [[Concepts/eventual-consistency]] and [[Concepts/linearizability]]).

A second cross-domain pattern: the "no free lunch" principle. Dopamine stacking shifts the cost to the baseline — the immediate reward is real but the long-term capacity for motivation erodes. This mirrors the "schemaless" database insight: document stores don't eliminate schema enforcement, they shift it to application code. In both cases, the trade-off disappears from view without disappearing.

The growth mindset's neurochemical basis — accessing dopamine from effort rather than outcome — is the Psychology analog of the distributed systems insight that the mechanism of correctness (how a guarantee is implemented) shapes the system's properties. Framing the process as the reward literally changes what triggers dopamine release, just as a system's isolation mechanism (2PL vs. SSI) literally changes what anomalies are possible.

## Open edges

- Sleep and dopamine: how does sleep architecture affect baseline recovery and peak dynamics?
- Stress and cortisol: what is the relationship between stress hormones and dopamine dynamics?
- Atypical dopamine systems: how do ADHD and depression interact with the baseline/peak framework?
- Intrinsic motivation theory (Deci & Ryan, self-determination theory): where does it align with or diverge from the neurochemical model?
- Habit formation: dopamine implies habits encode expected reward levels; how does this connect to habit research (Duhigg, Clear)?
