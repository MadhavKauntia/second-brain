---
title: Madhav's Second Brain
---
# Madhav's Second Brain

This is my knowledge garden, built from everything I'm reading, studying, and thinking through as a software engineer, poker player, and compulsive rabbit-hole follower.

It has two layers. The [[index|synthesized layer]] ([[Concepts/|concepts]], [[Topics/|topics]], and [[Sources/|sources]]) is maintained by an LLM as I ingest new material, based on [Andrej Karpathy's LLM-wiki pattern]([https://karpathy.ai](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)). The [[Notes/|notes]] are unfiltered notes from books and courses, the source material the synthesis is built on. Each source page links back to the notes behind it.

The synthesized layer gets richer every time I read something new. The notes stay as they were written.

---

## Notes

- [[Notes/Engineering/designing-data-intensive-applications|Designing Data-Intensive Applications]] — Kleppmann reading notes; distributed data systems
- [[Notes/Engineering/java-concurrency-in-practice|Java Concurrency in Practice]] — Goetz et al. reading notes; JVM concurrency fundamentals
- [[Notes/Psychology/dopamine|Dopamine]] — Huberman Lab; dopamine mechanics, motivation, and reward design

## Sources

- [[Sources/designing-data-intensive-applications|Designing Data-Intensive Applications]] — Kleppmann's comprehensive treatment of distributed data systems: storage, replication, partitioning, transactions, and consistency
- [[Sources/java-concurrency-in-practice|Java Concurrency in Practice]] — Goetz et al. on JVM concurrency fundamentals: thread safety, atomicity, locking, memory visibility and reordering
- [[Sources/dopamine|Controlling Your Dopamine For Motivation, Focus and Satisfaction]] — Huberman on dopamine's role in motivation, the baseline/peak ratio, and reward design

## Concepts

- [[Concepts/reliability-scalability-maintainability|Reliability, Scalability, and Maintainability]] — The three foundational properties of data-intensive systems; fault taxonomy
- [[Concepts/data-models|Data Models]] — Relational vs. document vs. graph models; schema-on-read vs. schema-on-write
- [[Concepts/storage-indexes|Storage Engines and Indexes]] — Hash indexes, SSTables, LSM-trees, B-trees; OLTP vs. OLAP; column-oriented storage
- [[Concepts/replication|Replication]] — Single-leader, multi-leader, leaderless replication; failover; replication log implementations
- [[Concepts/eventual-consistency|Eventual Consistency and Replication Lag]] — Replication lag anomalies: stale reads, non-monotonic reads, causal violations
- [[Concepts/partitioning|Partitioning]] — Key-range vs. hash partitioning; secondary indexes; rebalancing strategies
- [[Concepts/transactions-acid|Transactions and ACID]] — ACID guarantees; what "C" in ACID actually means
- [[Concepts/isolation-levels|Isolation Levels]] — Read committed, snapshot isolation, serializability; 2PL, SSI; lost updates, write skew
- [[Concepts/distributed-faults|Distributed Systems Faults]] — Partial failures, unreliable networks, unreliable clocks, Byzantine faults
- [[Concepts/linearizability|Linearizability and CAP Theorem]] — Recency guarantee; CAP theorem; which replication strategies are linearizable
- [[Concepts/thread-safety|Thread Safety]] — What thread safety means; shared mutable state; three strategies
- [[Concepts/atomicity|Atomicity]] — Indivisible operations; read-modify-write races; program-level vs. database-level atomicity
- [[Concepts/locking|Locking]] — Intrinsic locks, reentrancy, lock-guarding discipline; database 2PL; deadlock
- [[Concepts/memory-visibility|Memory Visibility and Reordering]] — JVM memory model; reordering; volatile variables
- [[Concepts/dopamine|Dopamine]] — Baseline/peak ratio; the readily-releasable pool; cold water's unique profile; caffeine vs. stimulants
- [[Concepts/motivation-maintenance|Motivation Maintenance]] — Intermittent reinforcement; reward stacking problem; process rewards; burnout as baseline suppression

## Topics

- [[Topics/distributed-systems|Distributed Systems]] — Theory and engineering of multi-machine systems; consistency, availability, fault tolerance
- [[Topics/databases|Databases]] — Storage engines, data models, transactions, and distributed databases
- [[Topics/concurrent-programming|Concurrent Programming]] — Thread safety, locking, memory visibility; program-level concurrency on the JVM
- [[Topics/psychology|Psychology]] — Neuroscience of motivation and behavior; dopamine mechanics and reward design

---

[[reading|Reading List]]
