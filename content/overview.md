---
title: Overview
type: overview
created: 2026-04-11
updated: 2026-04-11
raw: [Engineering/designing-data-intensive-applications.md, Engineering/java-concurrency-in-practice.md]
related: [topics/distributed-systems, topics/databases]
---

# Overview

*The wiki maintains this page. It reflects the current best synthesis across everything in the wiki. Updated whenever a new source meaningfully shifts the picture.*

---

## Current thesis

Data-intensive systems are fundamentally about trade-offs under adversity. Hardware fails, networks drop, clocks drift, and humans misconfigure — these are not edge cases but the baseline operating conditions of any system at scale. Every design decision (data model, storage engine, replication strategy, isolation level) is a navigation between reliability, performance, consistency, and availability. There is no free lunch, and the correct answer always depends on the specific access patterns and failure tolerance requirements of the application.

## Strongest ideas so far

- **The consistency spectrum is concrete.** Both in distributed replication (eventual → linearizable) and in local transactions (read committed → snapshot → serializable), the spectrum represents specific, enumerable anomalies — not vague notions of "correctness." Knowing which anomalies an application can tolerate is more useful than knowing the label of the consistency model.
- **"Schemaless" is a myth.** Document databases don't eliminate schemas; they shift schema enforcement from the database into application code (schema-on-read). This is sometimes a good trade-off, but it is never "schema-free."
- **Storage engine design predetermines the write/read profile.** LSM-trees optimize for write throughput with sequential I/O and background compaction; B-trees optimize for predictable read performance with in-place updates. Analytical (OLAP) workloads benefit from columnar storage entirely distinct from both.
- **CAP theorem is a consequence of linearizability's cost.** A linearizable system must sacrifice availability during network partitions. This is not a design choice but a mathematical constraint of recency guarantees in an asynchronous network.
- **Atomicity and visibility are the same problems at every layer of the stack.** JCIP's read-modify-write race (program level) and DDIA's lost update (database level) are structurally identical. JCIP's memory visibility failures (stale CPU-cached reads) and DDIA's replication lag anomalies (stale replica reads) are structurally identical. The mechanisms differ (locks vs. MVCC, volatile vs. quorum reads) but the underlying concerns — atomicity and visibility under concurrent access — run from the CPU cache all the way up to geographically distributed replicas.

## Active tensions

- **Weak isolation vs. application complexity.** Most production databases use snapshot isolation by default, not full serializability. This pushes the burden of handling lost updates and write skew onto the application. Many teams don't know this trade-off is being made.
- **Multi-leader replication enables write availability but makes consistency hard.** There is no principled conflict resolution strategy that is both correct and simple. The source acknowledges this tension but doesn't resolve it.

## Open edges

- **Consensus algorithms** (Raft, Paxos) — linearizable systems require them, but this wiki doesn't yet cover how they work
- **Event sourcing and CQRS** — how the "log as truth" pattern interacts with the encoding/evolution and storage principles from DDIA
- **HTAP** — whether the row/columnar storage split is fundamental, or whether modern systems can bridge OLTP and OLAP in one engine
- **Byzantine fault tolerance in practice** — beyond theory, where in the datacenter engineering world does this model actually apply?
- **Higher-level JVM concurrency** — `java.util.concurrent`, `happens-before`, lock-free structures, Kotlin coroutines — the JCIP notes only cover fundamentals
- **The `happens-before` formal model** — JCIP implies it but doesn't name it; this is the formal backbone connecting visibility and ordering guarantees across both JVM and distributed systems

---

*Last updated: 2026-04-11*
