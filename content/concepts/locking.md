---
title: Locking
type: concept
created: 2026-04-11
updated: 2026-04-11
notes: [notes/Engineering/java-concurrency-in-practice.md, notes/Engineering/designing-data-intensive-applications.md]
related: [concepts/thread-safety, concepts/atomicity, concepts/memory-visibility, concepts/isolation-levels, topics/concurrent-programming, topics/databases, sources/java-concurrency-in-practice, sources/designing-data-intensive-applications]
---

# Locking

## What it is

A lock is a synchronization primitive that allows at most one thread (or transaction) to hold it at a time. Any other thread that wants the lock must wait until it is released. Locking enforces mutual exclusion over a critical section — a block of code that accesses shared state.

### Program-level locking (Java intrinsic locks)

Java's `synchronized` keyword acquires an *intrinsic lock* (also called a monitor lock) on entry and releases it on exit, even if an exception is thrown. Every Java object has an associated intrinsic lock. Intrinsic locks are mutually exclusive: at most one thread holds any given lock at a time.

**Reentrancy**: a thread that already holds an intrinsic lock can re-acquire it without blocking. This is essential for inheritance — if a synchronized method calls another synchronized method on the same object, reentrancy prevents the thread from deadlocking itself. Implemented by associating each lock with an owner thread and an acquisition count; the count increments on re-acquisition and decrements on exit.

**The lock-guarding discipline** (the set of rules that make synchronization work correctly):
- Every shared mutable variable must be guarded by exactly one lock — always the same lock for that variable.
- All variables that participate in a compound invariant must be guarded by the same lock. If updating variable A and variable B must be done together to preserve a class invariant, both must be inside the same synchronized block.

Violating the guarding discipline — using lock A for reads and lock B for writes, or guarding related variables with different locks — leaves race conditions open even though locks are present.

### Database-level locking (DDIA)

Database locking is used to implement isolation levels:
- **Two-Phase Locking (2PL)**: the classic serializable isolation mechanism. Shared locks for reads, exclusive locks for writes. A transaction holds all locks until it commits. Readers block writers; writers block readers.
- **Row-level locks** (for read committed isolation): prevent dirty writes. Writers hold a lock on a row; other writers must wait. Readers are typically not blocked (implemented by returning the previously committed value rather than waiting for the lock).
- **Predicate locks / index-range locks**: lock all rows matching a search condition, to prevent phantom reads.

The structure parallels program-level locking: the same rules (exclusive access to shared mutable state, compound invariants require the same lock) apply, just at the transaction boundary rather than the method boundary.

## Why it matters

Locking is the fundamental tool for making compound operations atomic and ensuring consistent state across related variables. Without a discipline for *which* lock guards *which* state, the mere presence of locks provides no safety guarantee.

The discipline is where most bugs live: a variable guarded by different locks in different methods, a compound check-then-act where the check and act are separately synchronized, or a class where the invariant spans two fields guarded by two different locks. Each of these is thread-unsafe despite using locks.

## Evidence & examples

From [[sources/java-concurrency-in-practice]]:
- The one-lock-per-variable rule: if a `Map` and a separate `count` field must stay in sync, both must be guarded by the same lock — not separate synchronized blocks.
- Reentrancy preventing deadlock: `parent.synchronized` calls `child.synchronized`; if reentrancy didn't exist, the child calling back into parent would deadlock.

From [[sources/designing-data-intensive-applications]]:
- 2PL (database locking) produces serializable isolation — the strongest guarantee — but at a significant performance cost. Transaction throughput and query latency are substantially worse than under snapshot isolation.
- Index-range locks are a practical approximation of predicate locks: instead of locking "all rows where room = 123 and time overlaps 12–1pm", lock the range in the room_id index covering room 123. Coarser than predicate locks, but cheaper.

## Tensions & counterarguments

- **Locking can serialize operations that didn't need to be serialized.** A coarse-grained lock (e.g., synchronizing on `this` for an entire class) may block reads that are safe to run concurrently with other reads. Fine-grained locking (one lock per row, one lock per field) improves concurrency but increases deadlock risk.
- **Deadlock**: if thread A holds lock 1 and waits for lock 2, while thread B holds lock 2 and waits for lock 1, both wait forever. Preventing deadlock requires consistent lock ordering, lock timeouts, or deadlock detection. Database 2PL implementations detect deadlock by looking for cycles in the wait-for graph and aborting one transaction.
- **Volatile is not locking.** A `volatile` variable ensures visibility but not atomicity for compound operations. `if (flag) { doSomething(); }` is not safe under volatile even if `flag` is volatile — the check-then-act is still two operations.
- **Lock-free alternatives** (CAS-based data structures, `java.util.concurrent`) can outperform locking under contention but are harder to reason about and compose.

## Related

- [[concepts/thread-safety]] — locking is the tool for achieving thread safety when sharing mutable state
- [[concepts/atomicity]] — locking makes compound operations atomic
- [[concepts/memory-visibility]] — locking also provides visibility (a side effect not just mutual exclusion)
- [[concepts/isolation-levels]] — database-level locking (2PL, row locks, predicate locks)
- [[topics/concurrent-programming]] — broader concurrent programming context
