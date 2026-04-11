---
title: Isolation Levels
type: concept
created: 2026-04-11
updated: 2026-04-11
notes: [notes/Engineering/designing-data-intensive-applications.md, notes/Engineering/java-concurrency-in-practice.md]
related: [concepts/transactions-acid, concepts/eventual-consistency, concepts/locking, concepts/atomicity, topics/databases, topics/concurrent-programming, sources/designing-data-intensive-applications, sources/java-concurrency-in-practice]
---

# Isolation Levels

## What it is

Isolation levels define how much concurrency is allowed between simultaneous transactions, and consequently, what anomalies are possible. They form a ladder from weakest to strongest:

### Read Committed

The baseline in many production databases (PostgreSQL default, Oracle, SQL Server). Guarantees:
1. **No dirty reads** — you only read committed data. A transaction's writes are invisible to others until it commits.
2. **No dirty writes** — you only overwrite committed data. Row-level locks prevent two transactions from writing the same row concurrently.

Still allows: non-repeatable reads (reading the same row twice in one transaction may return different values if another transaction committed in between), phantom reads.

### Snapshot Isolation (Repeatable Read)

Each transaction reads from a consistent snapshot of the database as it was at the transaction's start time. Uses multi-version concurrency control (MVCC) — the database keeps multiple versions of each row. Writers don't block readers; readers don't block writers. Called "repeatable read" in MySQL.

Still allows: lost updates (two transactions read a value, both increment it, one increment is lost), write skew, phantoms.

**Preventing lost updates:**
- **Atomic write operations** (`UPDATE counter SET value = value + 1`) — the database handles read-modify-write atomically
- **Explicit locking** (`SELECT FOR UPDATE`) — application acquires a lock before reading
- **Automatic lost update detection** — the database detects and aborts conflicting transactions (used by PostgreSQL's REPEATABLE READ)
- **Compare-and-set** — only update if the value hasn't changed since you read it (not safe if the DB uses MVCC and reads from an old snapshot)

**Write skew and phantoms:** A write in one transaction can make the precondition of another transaction false, even when the two transactions don't write the same row. Example: two doctors both check "are there at least 2 doctors on call?" and both see yes, so both go off-call — leaving no doctor on call. This is write skew. The general pattern:
1. SELECT checks some condition
2. Application decides to proceed based on the result
3. Application writes to the database

A *phantom* is when a write changes the *set of rows* that a search condition would match. Materializing conflicts (creating explicit lock rows) is one approach; predicate locks are another.

### Serializability

The strongest guarantee: the result of executing transactions concurrently is the same as if they had run serially, one at a time. Prevents all anomalies. Three implementations:

**Actual serial execution**: run one transaction at a time, on a single thread. Fast enough only when: the active dataset fits in memory, transactions are short and don't do I/O in the middle, write throughput fits a single CPU core. Used by Redis, VoltDB, H-Store.

**Two-Phase Locking (2PL)**: the traditional serializable implementation. Writers block readers and other writers; readers block writers (unlike snapshot isolation where readers and writers don't block each other). A lock on each object is held in shared mode (read) or exclusive mode (write). *Predicate locks* lock all rows matching a search condition. *Index-range locks* are a practical approximation — lock an index range rather than a precise predicate. Performance cost is significant; this is why serializable isolation wasn't universally adopted in the 70s.

**Serializable Snapshot Isolation (SSI)**: optimistic concurrency control. Transactions proceed without blocking, using snapshot isolation. At commit time, the database checks if the transaction's preconditions were violated (e.g., a row it read was modified by a concurrent transaction). If so, the transaction is aborted and must retry. SSI has much better read performance than 2PL — readers never block. Works well when transactions rarely conflict.

## Why it matters

Isolation level choice is one of the most consequential and least visible decisions in database-backed application design. Most developers assume their database prevents all concurrency anomalies; few know which isolation level is actually in use. Bugs caused by weak isolation are hard to reproduce and often surface only under production load.

The spectrum — read committed → snapshot → serializable — represents real trade-offs: each stronger level prevents more anomalies but adds overhead (locks, abort rate, latency). There is no universally correct choice; the right level depends on what concurrency anomalies the application can tolerate.

## Evidence & examples

From [[sources/designing-data-intensive-applications]]:

- Snapshot isolation + backup failure case: during a multi-hour backup, some rows are from earlier versions — inconsistencies become permanent if restored
- Write skew (on-call doctors) illustrates how two transactions can each do the right thing individually but violate an invariant together
- Meeting room booking as a classic phantom example: no single row is double-written, but a logical constraint (room availability) is violated
- SSI's optimism works well in low-contention workloads; under high contention (many aborts and retries), it degrades

## Tensions & counterarguments

- **2PL has decades of production use** but throughput and latency are significantly worse than snapshot isolation. Most applications choose snapshot isolation and accept the residual risks of lost updates and write skew.
- **SSI is promising but less battle-tested** than 2PL. The abort-and-retry behavior can be surprising and hard to reason about in the presence of long-running transactions.
- **Snapshot isolation is named inconsistently** across databases: "repeatable read" in MySQL means snapshot isolation; in SQL standard, it means something different. Always verify what the database actually implements.

## Related

- [[concepts/transactions-acid]] — the ACID model within which isolation levels operate
- [[concepts/eventual-consistency]] — the distributed analogue to isolation levels; similar anomaly spectrum
- [[concepts/locking]] — 2PL is the database-level implementation of locking for serializable isolation; mirrors Java's intrinsic lock discipline in [[sources/java-concurrency-in-practice]]
- [[concepts/atomicity]] — lost updates are a failure of atomicity at the transaction level; JCIP's read-modify-write race is the same problem at the program level
- [[topics/databases]] — broader database engineering context
- [[topics/concurrent-programming]] — program-level concurrency; the same atomicity and visibility concerns apply at the thread level
