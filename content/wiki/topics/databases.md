---
title: Databases
type: topic
created: 2026-04-11
updated: 2026-04-11
sources: [Engineering/designing-data-intensive-applications.md]
related: [wiki/concepts/data-models, wiki/concepts/storage-indexes, wiki/concepts/transactions-acid, wiki/concepts/isolation-levels, wiki/concepts/replication, wiki/concepts/partitioning, wiki/topics/distributed-systems]
---

# Databases

## What this topic covers

The engineering of data storage and retrieval systems — from single-node storage engines and data models to distributed transactional databases and analytical warehouses.

## Core concepts

- [[wiki/concepts/data-models]] — relational, document, graph models and the trade-offs between them
- [[wiki/concepts/storage-indexes]] — how databases physically store data (B-trees, LSM-trees) and how indexes speed up queries
- [[wiki/concepts/transactions-acid]] — what transactions are, what ACID guarantees mean, and what they cost
- [[wiki/concepts/isolation-levels]] — the spectrum from weak to strong transaction isolation and the concurrency anomalies each allows

## Key sources

- [[wiki/sources/designing-data-intensive-applications]] — the primary source for this topic; covers storage engines, data models, transactions, and distributed databases comprehensively

## Synthesis

Databases are the dominant abstraction for durably storing structured data, and their design reflects a series of deliberate trade-offs. The most important divisions:

**Data model:** Relational (SQL), document (JSON/BSON), and graph models each have a home. Relational generalizes well for normalized data with many-to-many relationships. Document models reduce impedance mismatch for hierarchical data but shift schema enforcement to the application. Graph models handle highly interconnected data naturally. The key insight from DDIA: "schemaless" document stores are not schema-free — they have implicit, application-enforced schemas.

**Storage engine:** Log-structured (LSM-tree) engines optimize for write throughput with background compaction; page-oriented (B-tree) engines optimize for consistent read performance with in-place updates. OLTP systems use row-oriented storage; OLAP analytical systems use column-oriented storage and compression to scan large datasets efficiently.

**Transactions:** ACID transactions provide correctness guarantees for concurrent writes. The "C" in ACID is the application's responsibility. The real complexity is isolation: the isolation level determines which concurrency anomalies (dirty reads, lost updates, write skew, phantoms) are possible. Most databases default to read committed or snapshot isolation — not full serializability — for performance reasons.

**Distribution:** When a single machine is insufficient, databases use replication (for fault tolerance and read scaling) and partitioning (for write scaling and data volume). Both introduce consistency complexity. See [[wiki/topics/distributed-systems]].

## Open edges

- How do modern NewSQL databases (Spanner, CockroachDB, YugabyteDB) implement serializable isolation across distributed partitions without sacrificing too much latency?
- What are the operational realities of SSI (Serializable Snapshot Isolation) in production? How do abort rates behave under real-world workloads?
- How does event sourcing (treating the log as the source of truth) interact with these storage engine and encoding principles?
- HTAP (Hybrid Transactional/Analytical Processing): can one engine serve both OLTP and OLAP efficiently, or is the row/column storage split fundamental?
