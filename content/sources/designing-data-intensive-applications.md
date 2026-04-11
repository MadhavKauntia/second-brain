---
title: Designing Data-Intensive Applications
type: source
created: 2026-04-11
updated: 2026-04-11
notes: Notes/Engineering/designing-data-intensive-applications.md
related: [Concepts/reliability-scalability-maintainability, Concepts/data-models, Concepts/storage-indexes, Concepts/replication, Concepts/eventual-consistency, Concepts/partitioning, Concepts/transactions-acid, Concepts/isolation-levels, Concepts/distributed-faults, Concepts/linearizability, Topics/distributed-systems, Topics/databases]
---

# Designing Data-Intensive Applications

**Type:** book
**Domain:** Engineering
**Ingested:** 2026-04-11
**Notes:** [[Notes/Engineering/designing-data-intensive-applications|My notes while reading]]

## Summary

DDIA (by Martin Kleppmann) is a comprehensive treatment of the engineering challenges behind systems that move, store, and transform large amounts of data. The book's central thesis is that distributed data systems are fundamentally hard because of three inescapable realities: hardware and software fail unpredictably, networks are asynchronous and lossy, and clocks across machines cannot be reliably synchronized. Every design decision in a data-intensive system is ultimately a navigation of trade-offs between reliability, consistency, availability, and performance.

The first half builds from the ground up: data models (relational, document, graph), storage engines (log-structured vs. page-oriented), encoding formats, and replication/partitioning strategies. The second half deepens into distributed systems theory — what it means for a system to be "correct," why transactions are harder in distributed settings, and what consistency guarantees are actually achievable. The book treats CAP theorem not as a trilemma to memorize but as a consequence of linearizability's cost under network partitions.

A recurring insight is that many "simple" abstractions leak. Document databases are not "schemaless" — they shift schema enforcement into application code. "Eventual consistency" is not one thing — it's a family of weak guarantees that differ significantly in what anomalies they permit. The isolation level ladder (dirty reads → read committed → snapshot → serializable) represents real, practical trade-offs, not just academic taxonomy.

## Key ideas

- **Reliability, Scalability, Maintainability** are the three foundational properties of data-intensive applications. Faults are classified as hardware, software, or human; fault-tolerance means surviving them, not preventing them.
- **Data models** shape application complexity: document models fit hierarchical one-to-many data; relational models handle many-to-many; graph models handle highly interconnected data.
- **Declarative vs. imperative query languages** — declarative (SQL, CSS) are more amenable to optimization and parallelism than imperative code.
- **Storage engines** split into log-structured (LSM-trees: fast writes, background compaction) and page-oriented (B-trees: consistent read performance, writes slower). Each has distinct performance profiles.
- **OLTP vs. OLAP** systems have different access patterns; data warehouses with columnar storage and bitmap encoding serve analytical queries far more efficiently than row-oriented stores.
- **Encoding & evolution** — backward/forward compatibility requires discipline; schema-based binary formats (Avro, Protobuf) provide stronger guarantees than ad-hoc JSON.
- **Replication** strategies — single-leader (simple, read scaling), multi-leader (write availability, conflict complexity), leaderless (Dynamo-style, quorum-based). Replication lag introduces consistency anomalies: stale reads, non-monotonic reads, causal violations.
- **Partitioning** — key-range (range scan friendly, hot spot risk) vs. hash (even distribution, loses range queries). Secondary indexes complicate routing.
- **Transactions and ACID** — Atomicity prevents partial writes; Consistency is application-defined invariants; Isolation prevents concurrency anomalies; Durability ensures committed writes survive crashes.
- **Isolation levels** form a ladder: read committed → repeatable read (snapshot isolation) → serializable. Weak levels allow dirty reads, lost updates, write skew, and phantoms.
- **Serializability** is implementable via actual serial execution, two-phase locking (2PL), or serializable snapshot isolation (SSI). SSI is optimistic and avoids lock contention.
- **Distributed systems** face partial failures (nondeterministic), unreliable networks (asynchronous with unbounded delay), and unreliable clocks (NTP drift, leap seconds, GC pauses).
- **Linearizability** is a recency guarantee — behaves as if one copy of data. Required for leader election, uniqueness constraints. Its cost is unavailability under network partitions (CAP theorem).

## Connections

- [[Concepts/reliability-scalability-maintainability]] — the source's opening framework for evaluating systems.
- [[Concepts/data-models]] — foundational chapter on relational, document, and graph models and when to use each.
- [[Concepts/storage-indexes]] — detailed treatment of how databases physically store and retrieve data.
- [[Concepts/replication]] — how distributed databases keep multiple copies consistent.
- [[Concepts/eventual-consistency]] — replication lag and its anomalies; the weakness of "eventual" as a guarantee.
- [[Concepts/partitioning]] — strategies for distributing data across nodes.
- [[Concepts/transactions-acid]] — what transactions actually mean and what ACID guarantees.
- [[Concepts/isolation-levels]] — the spectrum from weak to strong isolation and the race conditions each allows.
- [[Concepts/distributed-faults]] — the theory of what can go wrong in distributed systems (partial failures, Byzantine faults, system models).
- [[Concepts/linearizability]] — the strong consistency guarantee and its connection to CAP theorem.

## Open questions

- How does Dynamo-style leaderless replication hold up against Byzantine fault models? The book treats "lying nodes" as a separate chapter but doesn't deeply connect the two.
- SSI is described as promising but the book doesn't cover its production limitations in depth — what are the real-world deployment constraints?
- How do the encoding/evolution principles interact with event sourcing patterns, where the log is the source of truth?
- Column-oriented storage is described for read-heavy analytics — how do modern hybrid transactional/analytical processing (HTAP) databases handle this trade-off?
