---
title: Scaling
type: concept
created: 2026-04-11
updated: 2026-04-11
notes: []
related: [Concepts/delegation, Concepts/partitioning, Concepts/replication, Topics/system-design, Sources/system-design-masterclass-01]
---

# Scaling

## What it is

Scaling is a system's ability to handle increasing load — more users, more requests, more data. The two primary strategies are vertical scaling (making individual machines more powerful) and horizontal scaling (adding more machines).

**Vertical scaling** adds CPU, RAM, or disk to existing machines. Simple to manage, no architectural changes required, but has a hard ceiling and creates single-point-of-failure risk.

**Horizontal scaling** adds machines. Theoretically unbounded capacity, but introduces complexity: state coordination, network partitioning, and fault tolerance become active concerns.

## Why it matters

Horizontal scaling is often presented as the default correct answer, but the key constraint is that **stateful components** — databases and caches — do not scale the same way stateless components do. Stateless API servers can be replicated freely; stateful systems require coordination that limits throughput.

The practical implication: **scale bottom-up**. A stateless API tier sitting in front of a saturated DB gains nothing from adding more API servers. Fix the bottleneck where it actually is.

## Evidence & examples

From [[Sources/system-design-masterclass-01]]: vertical scaling is the "hulk" strategy (powerful, limited); horizontal is "minions" (many, distributed, complex). The bottleneck is always the stateful layer — API servers are easy to replicate, databases and caches are not.

DDIA's treatment of [[Concepts/partitioning]] and [[Concepts/replication]] is the deeper treatment of the same problem — both are mechanisms for horizontal scaling of stateful data, each with specific trade-offs around consistency and availability.

## Tensions & counterarguments

- Scaling API servers when the DB is the bottleneck is waste. The instinct to add more servers is often wrong.
- "Infinite horizontal scaling" is a simplification — horizontal scaling of stateful systems requires partitioning, replication, and coordination strategies, each introducing consistency trade-offs (see [[Concepts/linearizability]], [[Concepts/eventual-consistency]]).

## Related

- [[Concepts/delegation]] — offloading work asynchronously reduces synchronous demand; a complement to capacity scaling
- [[Concepts/partitioning]] — DDIA's treatment of horizontal data distribution
- [[Concepts/replication]] — DDIA's treatment of data copies across machines
- [[Topics/system-design]]
