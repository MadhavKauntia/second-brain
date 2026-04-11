---
title: Atomicity
type: concept
created: 2026-04-11
updated: 2026-04-11
sources: [Engineering/java-concurrency-in-practice.md, Engineering/designing-data-intensive-applications.md]
related: [wiki/concepts/thread-safety, wiki/concepts/locking, wiki/concepts/transactions-acid, wiki/concepts/isolation-levels, wiki/topics/concurrent-programming, wiki/topics/databases, wiki/sources/java-concurrency-in-practice, wiki/sources/designing-data-intensive-applications]
---

# Atomicity

## What it is

An operation (or group of operations) is *atomic* if it executes as a single, indivisible unit from the perspective of all other threads or processes. Either it completes fully, or it doesn't happen at all — no intermediate state is observable.

Atomicity appears at multiple layers of the stack, with the same underlying concept but different mechanisms:

**Program-level atomicity (JCIP):** At the instruction level, operations that look single-step in source code may be multi-step in machine code. `++count` compiles to three operations: read the current value, add one, write the result back. Two threads executing this concurrently can both read `9`, both compute `10`, and both write `10` — silently losing one increment. This is a *read-modify-write race*. Atomic variables (`AtomicLong`, `AtomicReference`) use CPU-level compare-and-swap (CAS) instructions to make these operations indivisible without locking.

**Database-level atomicity (ACID):** In transactions, atomicity means a group of reads and writes either all commit or all roll back. If a write fails mid-transaction, no partial changes persist. This is not about indivisibility in the CAS sense — it is about all-or-nothing completion for a logical unit of work, implemented via a write-ahead log (WAL).

These two senses share the abstraction of "no visible intermediate state" but differ in scope and mechanism:
- Program-level: a single memory operation, enforced by hardware (CAS) or locks
- Database-level: a transaction spanning multiple rows/tables, enforced by logging and rollback

## Why it matters

Atomicity violations are among the most common and most subtle concurrency bugs. They occur whenever a "single" operation in application logic is actually multiple operations in the underlying runtime, and another thread can observe or modify the shared state in between.

The read-modify-write pattern — read a value, compute a new value based on it, write the result — is almost never atomic unless explicitly made so. This pattern appears constantly: incrementing counters, checking and setting flags, reading from one field to write another. Every occurrence is a potential race condition under concurrent access.

## Evidence & examples

From [[wiki/sources/java-concurrency-in-practice]]:
- `++count` race: two threads read `9`, both write `10`, increment is lost. Fix: use `AtomicLong.incrementAndGet()` which uses CAS, or protect with a lock.
- The rule: "update *related* state variables in a single atomic operation" — if a class maintains a cached result and a flag indicating the cache is valid, both must be updated atomically or another thread can read an inconsistent combination.

From [[wiki/sources/designing-data-intensive-applications]]:
- DDIA's "lost update" problem is the same read-modify-write race, but at the database transaction level: two transactions each read a value, increment it, and write it back — one increment is lost.
- DDIA's solution mirrors JCIP's: use database atomic operations (`UPDATE counter SET value = value + 1`) or explicit locking (`SELECT FOR UPDATE`), exactly paralleling JCIP's `AtomicLong` or `synchronized`.
- Compare-and-set in DDIA (`UPDATE ... WHERE content = 'old value'`) is the database-level equivalent of CAS at the program level.

## Tensions & counterarguments

- **Atomic operations compose poorly.** Two individually atomic operations are not atomic together. `if (map.containsKey(k)) { map.get(k); }` is not thread-safe even if `containsKey` and `get` are individually atomic — another thread can remove the key between the two calls. Composing atomic operations requires a lock covering the entire compound operation.
- **CAS is not always the answer.** Compare-and-swap loops under high contention degrade — many threads spinning on a CAS failure wastes CPU. Locks may actually be more efficient under high contention because they queue waiters rather than spinning.
- **ACID atomicity does not imply program-level atomicity.** A database transaction that rolls back does not undo the program-level state changes that happened in the application code before the rollback. The application may need to manually undo its own in-memory state.

## Related

- [[wiki/concepts/thread-safety]] — atomicity is one of the building blocks of thread-safe code
- [[wiki/concepts/locking]] — the general mechanism for making compound operations atomic
- [[wiki/concepts/transactions-acid]] — database-level atomicity; the all-or-nothing guarantee for a transaction
- [[wiki/concepts/isolation-levels]] — lost updates (a failure of atomicity at the transaction level) and how isolation levels address them
- [[wiki/topics/concurrent-programming]] — broader context for program-level concurrency
- [[wiki/topics/databases]] — broader context for database-level atomicity
