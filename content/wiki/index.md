# Wiki Index

*Master catalog. Updated on every ingest. Read this first when answering any query.*

**Stats:** 2 sources · 14 concepts · 3 topics · initialized 2026-04-11

---

## Sources
*One entry per ingested source.*

- [[wiki/sources/designing-data-intensive-applications|Designing Data-Intensive Applications]] — Kleppmann's comprehensive treatment of distributed data systems: storage, replication, partitioning, transactions, and consistency
- [[wiki/sources/java-concurrency-in-practice|Java Concurrency in Practice]] — Goetz et al. on JVM concurrency fundamentals: thread safety, atomicity, locking, memory visibility and reordering

---

## Concepts
*One entry per concept page.*

- [[wiki/concepts/reliability-scalability-maintainability|Reliability, Scalability, and Maintainability]] — The three foundational properties of data-intensive systems; fault taxonomy (hardware, software, human)
- [[wiki/concepts/data-models|Data Models]] — Relational vs. document vs. graph models; schema-on-read vs. schema-on-write; declarative vs. imperative query languages
- [[wiki/concepts/storage-indexes|Storage Engines and Indexes]] — Hash indexes, SSTables, LSM-trees, B-trees; OLTP vs. OLAP; column-oriented storage
- [[wiki/concepts/replication|Replication]] — Single-leader, multi-leader, leaderless replication; failover; replication log implementations
- [[wiki/concepts/eventual-consistency|Eventual Consistency and Replication Lag]] — Replication lag anomalies: stale reads, non-monotonic reads, causal violations; read-your-own-writes
- [[wiki/concepts/partitioning|Partitioning]] — Key-range vs. hash partitioning; secondary indexes; rebalancing strategies; request routing
- [[wiki/concepts/transactions-acid|Transactions and ACID]] — ACID guarantees; single vs. multi-object transactions; what "C" in ACID actually means
- [[wiki/concepts/isolation-levels|Isolation Levels]] — Read committed, snapshot isolation, serializability; 2PL, SSI; lost updates, write skew, phantoms
- [[wiki/concepts/distributed-faults|Distributed Systems Faults]] — Partial failures, unreliable networks, unreliable clocks, Byzantine faults, system models, safety vs. liveness
- [[wiki/concepts/linearizability|Linearizability and CAP Theorem]] — Recency guarantee; CAP theorem; which replication strategies are linearizable
- [[wiki/concepts/thread-safety|Thread Safety]] — What thread safety means; shared mutable state; three strategies (no sharing, immutability, synchronization)
- [[wiki/concepts/atomicity|Atomicity]] — Indivisible operations; read-modify-write races; program-level vs. database-level atomicity; CAS
- [[wiki/concepts/locking|Locking]] — Intrinsic locks, reentrancy, lock-guarding discipline; database 2PL; deadlock
- [[wiki/concepts/memory-visibility|Memory Visibility and Reordering]] — JVM memory model; reordering; volatile variables; visibility as separate from mutual exclusion

---

## Topics
*Cross-cutting topic hubs.*

- [[wiki/topics/distributed-systems|Distributed Systems]] — Theory and engineering of multi-machine systems; consistency, availability, and fault tolerance trade-offs
- [[wiki/topics/databases|Databases]] — Storage engines, data models, transactions, and distributed databases
- [[wiki/topics/concurrent-programming|Concurrent Programming]] — Thread safety, locking, memory visibility; program-level concurrency on the JVM
