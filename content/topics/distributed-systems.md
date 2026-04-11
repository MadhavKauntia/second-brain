---
title: Distributed Systems
type: topic
created: 2026-04-11
updated: 2026-04-11
notes: [Notes/Engineering/designing-data-intensive-applications.md]
related: [Concepts/replication, Concepts/partitioning, Concepts/distributed-faults, Concepts/linearizability, Concepts/eventual-consistency, Topics/databases]
---

# Distributed Systems

## What this topic covers

The theory and engineering of systems that run across multiple machines connected by a network — where failures, delays, and inconsistency are normal operating conditions, not exceptions.

## Core concepts

- [[Concepts/replication]] — keeping copies of data on multiple nodes for fault tolerance and read scaling
- [[Concepts/partitioning]] — splitting data across nodes for horizontal scalability
- [[Concepts/distributed-faults]] — the failure modes specific to distributed systems: partial failures, unreliable networks, unreliable clocks, Byzantine faults
- [[Concepts/linearizability]] — the strongest consistency guarantee in distributed systems; its connection to CAP theorem
- [[Concepts/eventual-consistency]] — the weaker consistency family produced by asynchronous replication

## Key sources

- [[Sources/designing-data-intensive-applications]] — comprehensive treatment of distributed data systems, their failure modes, and the consistency/availability trade-offs

## Synthesis

Distributed systems are defined by three inescapable realities: **hardware and software fail unpredictably** (partial failures are normal); **networks are asynchronous** (messages are lost, delayed, or reordered with no upper bound on timing); and **clocks diverge** (no machine can fully trust timestamps from another machine, or even its own clock across a GC pause).

The central engineering challenge is defining what "correctness" means in this environment and building systems that maintain it. The two primary axes are:

1. **Consistency vs. availability**: a linearizable system that refuses to answer during a network partition is correct but unavailable. An eventually consistent system that serves stale data is available but potentially incorrect. Which trade-off is right depends entirely on what the application can tolerate.

2. **Safety vs. liveness**: distributed algorithms can always guarantee safety (nothing bad happens) even in the asynchronous model, but liveness (something good eventually happens) requires timing assumptions. Most real algorithms assume partial synchrony and guarantee liveness when the environment cooperates.

The mental model shift required for distributed systems: **suspicion and pessimism are correct**. A node cannot trust that it is still the leader, that its clock is accurate, or that a request it sent was processed. Systems must be designed to handle any node being declared dead at any time, any write arriving out of order, and any operation being retried.

Replication and partitioning are the two primary mechanisms for horizontal scaling — they are almost always used together. Their combination multiplies the complexity of coordination, consistency, and failure handling.

## Open edges

- How do HTAP (Hybrid Transactional/Analytical Processing) databases — which aim to eliminate the OLTP/OLAP separation — navigate the distributed consistency trade-offs?
- How does consensus (Raft, Paxos) work in detail, and what are the practical production constraints? DDIA introduces the need for consensus but doesn't detail the implementations.
- Byzantine fault tolerance and its practical applications beyond blockchain — where does this model actually matter in datacenter systems?
- How do distributed tracing and observability systems handle the clock synchronization problem across nodes when attributing latency to specific components?
