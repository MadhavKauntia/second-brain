---
title: Reliability, Scalability, and Maintainability
type: concept
created: 2026-04-11
updated: 2026-04-11
raw: [Engineering/designing-data-intensive-applications.md]
related: [concepts/distributed-faults, concepts/replication, topics/distributed-systems, topics/databases, sources/designing-data-intensive-applications]
---

# Reliability, Scalability, and Maintainability

## What it is

The three foundational properties that define a well-engineered data-intensive application:

- **Reliability**: the system continues to work correctly in the face of faults — hardware failures, software bugs, and human errors. Note the distinction: a *fault* is one component deviating from spec; a *failure* is the whole system stopping. Fault-tolerant systems anticipate faults and cope with them rather than trying to prevent them entirely.
- **Scalability**: as the system grows (data volume, traffic, or complexity), there are reasonable ways to handle that growth. Load is described by load parameters (requests/sec, read/write ratio, active users). Performance is measured by percentiles of response time, not averages — percentiles like p95/p99 are what SLOs and SLAs are built on.
- **Maintainability**: over time, many people will work on the system. Good systems are operable (operations teams can keep them running), simple (complexity is removed so new engineers can understand the system), and evolvable (changes can be made easily as requirements change).

## Why it matters

These three properties are not independent features to bolt on — they are frames that cut across every architectural decision. A system can be highly reliable but poorly maintainable. A system can be scalable in theory but hard to operate. Using these three axes as evaluation criteria forces clarity about trade-offs that are otherwise vague.

The reliability framing in particular is useful: rather than asking "will this fail?" (everything fails eventually), it asks "what happens when it fails?" Fault classification — hardware faults (hard disks, power), software faults (bugs triggered by unusual conditions), human errors (misconfiguration) — maps to different mitigation strategies. Human errors cause the majority of outages, which argues strongly for well-defined abstractions, staging environments, and detailed monitoring.

## Evidence & examples

From [[sources/designing-data-intensive-applications]]:

- Faults classified as hardware (random, independent), software (systematic, often correlated), and human (configuration mistakes, the most common cause of outages)
- Response time percentiles beat averages: p99 reveals tail latency that matters for SLAs; average hides it
- Maintainability decomposed into Operability, Simplicity, Evolvability — each with distinct engineering practices

## Tensions & counterarguments

- Reliability and scalability can conflict: making a system more available (e.g., adding replicas) introduces consistency complexity. See [[concepts/replication]] and [[concepts/eventual-consistency]].
- Simplicity and evolvability can conflict: the most evolvable systems sometimes need more abstraction layers, which increase apparent complexity for new engineers.
- There is no universal set of "right" load parameters — what matters depends entirely on the system's architecture. Choosing the wrong metric leads to optimizing for the wrong thing.

## Related

- [[concepts/distributed-faults]] — the adversarial conditions reliability must be designed against
- [[concepts/replication]] — one primary tool for reliability (fault tolerance via redundancy)
- [[topics/distributed-systems]] — broader context for why these properties are hard
- [[topics/databases]] — the domain where these trade-offs are most sharply felt
