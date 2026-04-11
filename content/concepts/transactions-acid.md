---
title: Transactions and ACID
type: concept
created: 2026-04-11
updated: 2026-04-11
raw: [Engineering/designing-data-intensive-applications.md, Engineering/java-concurrency-in-practice.md]
related: [concepts/isolation-levels, concepts/storage-indexes, concepts/atomicity, concepts/distributed-faults, topics/databases, topics/concurrent-programming, sources/designing-data-intensive-applications, sources/java-concurrency-in-practice]
---

# Transactions and ACID

## What it is

A transaction groups multiple reads and writes into a logical unit: either all succeed (commit) or all are rolled back (abort). Transactions are the primary tool databases provide for handling partial failures and concurrency safely.

**ACID** is the classic set of transaction guarantees:

- **Atomicity**: all writes in a transaction are applied, or none are. The ability to abort and discard all writes from a failed transaction is the defining feature. Atomicity prevents partial writes from leaving the database in an inconsistent intermediate state.
- **Consistency**: certain invariants about the data (application-defined, not database-defined) are always true. Example: debits and credits always sum to zero. Importantly, this is the *application's* responsibility to define — the database can only enforce invariants it knows about (e.g., foreign key constraints, uniqueness). Consistency is the one "C" in ACID that is actually a property of the application, not the database.
- **Isolation**: concurrently executing transactions are isolated from each other — they cannot observe each other's partial writes. Full isolation means serializability (transactions behave as if executed one at a time). In practice, databases often provide weaker isolation levels for performance reasons. See [[concepts/isolation-levels]].
- **Durability**: once a transaction commits, its writes are permanent, even in the face of hardware faults or crashes. Implemented via write-ahead logs (WAL) and replication.

### Single-object vs. multi-object transactions

Single-object writes can benefit from atomicity and isolation (a log for crash recovery, a lock for isolation), but they are not transactions in the full sense. True multi-object transactions are needed when:
- Foreign key references must remain valid across tables
- Denormalized documents must be updated consistently across multiple places
- Secondary indexes must stay in sync with primary data

Many NoSQL databases lack multi-object transactions, forcing applications to handle partial updates and inconsistency themselves.

## Why it matters

Transactions exist because applications cannot practically anticipate and handle every possible failure mode in distributed code. The alternative — manually handling partial failures, detecting and recovering from inconsistent states, implementing your own locking — is both complex and error-prone.

The ACID contract lets application developers reason about correctness without thinking about concurrent writes or hardware faults. The cost is performance (locks, coordination) and, in distributed databases, availability trade-offs. The trade-off is not whether to use transactions but *which level of guarantees* the application actually needs.

## Evidence & examples

From [[sources/designing-data-intensive-applications]]:

- The "C" in ACID is noted explicitly as the application's responsibility, not the database's — this is a commonly misunderstood point
- ORM frameworks can introduce unsafe read-modify-write cycles that bypass atomic operations; this is a subtle source of bugs invisible in testing
- Key feature of a transaction: it can be safely retried after an abort — "retry on abort" is how ACID databases handle transient failures

## Tensions & counterarguments

- **Transactions are not free.** Serializable isolation has significant throughput cost. Most databases default to weaker isolation levels (see [[concepts/isolation-levels]]) and trust developers to understand the trade-offs.
- **NoSQL's move away from transactions** was partly about scalability, but multi-object transactions in distributed systems are genuinely hard to implement correctly without sacrificing availability. Some systems (Spanner, CockroachDB) have solved this at significant complexity and latency cost.
- **Durability is not absolute.** WAL plus replication provides strong durability, but durability guarantees only hold within the durability contract. If the entire datacenter is destroyed, replicas in the same datacenter don't help.

## Related

- [[concepts/isolation-levels]] — the spectrum of isolation guarantees and the race conditions each level allows
- [[concepts/storage-indexes]] — WAL and B-tree structure underpin atomicity and durability
- [[concepts/distributed-faults]] — transactions in distributed systems face additional failure modes
- [[topics/databases]] — broader database engineering context
