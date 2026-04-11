---
title: System Design
type: topic
created: 2026-04-11
updated: 2026-04-11
notes: []
related: [Topics/distributed-systems, Topics/databases, Concepts/scaling, Concepts/delegation, Concepts/communication-protocols]
---

# System Design

## What this topic covers

The practice of designing large-scale software systems: choosing components, defining interfaces, estimating capacity, and making trade-offs across reliability, performance, and complexity.

## Core concepts

- [[Concepts/scaling]] — vertical vs. horizontal scaling; stateful bottlenecks; bottom-up scaling heuristic
- [[Concepts/delegation]] — async-by-default for non-real-time work; delegate and respond
- [[Concepts/communication-protocols]] — short polling, long polling, WebSocket, SSE

## Key sources

- [[Sources/system-design-masterclass-01]] — Lecture 01: foundational approach, online/offline indicator case study, survey of core topics

## Synthesis

Early-stage synthesis — the domain is just opening. The first lecture establishes two meta-principles that will likely recur:

1. **Requirements determine architecture.** The online/offline indicator example shows this sharply — adding "last seen" as a requirement invalidates a previously elegant storage optimization (TTL expiry). There is no universally correct design, only designs appropriate to specific requirements.

2. **Identify the bottleneck.** Scaling, delegation, and protocol choice all reduce to the same underlying question: where is the constraint? Stateless API servers are rarely the bottleneck. Stateful components usually are.

Both principles echo patterns already in the wiki. DDIA's entire treatment of distributed data is an extended answer to "where is the bottleneck in a data-intensive system?" The system design domain applies the same reasoning one level up — across the whole architecture rather than within a single storage component.

## Open edges

- Deeper treatment of each foundational topic (caching mechanics, DB selection criteria, concurrency patterns at scale) — coming in later lectures
- Load balancing and service discovery — not yet covered
- Capacity estimation methodology — the online/offline example shows napkin math; no systematic framework yet
- Failure modes and resilience patterns — horizontal scaling mentions fault tolerance but does not yet explore it
