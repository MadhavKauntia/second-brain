---
title: Java Concurrency in Practice
type: source
created: 2026-04-11
updated: 2026-04-11
sources: [Engineering/java-concurrency-in-practice.md]
related: [wiki/concepts/thread-safety, wiki/concepts/atomicity, wiki/concepts/locking, wiki/concepts/memory-visibility, wiki/concepts/isolation-levels, wiki/concepts/transactions-acid, wiki/topics/concurrent-programming]
---

# Java Concurrency in Practice

**Type:** book
**Domain:** Engineering
**Ingested:** 2026-04-11

## Summary

Java Concurrency in Practice (Goetz et al.) is the canonical reference for writing correct concurrent code on the JVM. The notes cover the Fundamentals section: what thread safety actually means, why atomicity is harder than it looks, how Java's intrinsic locks work, and why memory visibility is a separate problem from mutual exclusion.

The book's central framing is precise and important: thread safety is not about threads — it is about *shared, mutable state*. A class is thread-safe if it behaves correctly under arbitrary scheduling and interleaving, with no additional synchronization required from callers. This definition shifts the engineering question from "how do I manage threads?" to "what state is shared, and how is access to it coordinated?"

Three strategies achieve thread safety: don't share state (stateless objects are always thread-safe), make state immutable (immutable objects need no synchronization), or synchronize all access consistently. The synchronization discipline is non-trivial: every shared mutable variable must be guarded by exactly one lock, and all variables that participate in the same invariant must be guarded by the *same* lock.

The visibility section is the most surprising: even without conflicting writes, a reading thread is not guaranteed to see values written by another thread — the compiler and CPU are free to reorder memory operations. Locking provides both mutual exclusion *and* memory visibility. Volatile variables are a lighter-weight alternative, but with strict usage constraints.

## Key ideas

- **Thread safety is about shared, mutable state** — not threads. Stateless objects are unconditionally thread-safe.
- **Three paths to thread safety**: no sharing, immutability, or synchronization.
- **Atomicity**: operations that appear indivisible from the perspective of other threads. `++count` is a read-modify-write and is *not* atomic — two threads can both read 9 and both write 10.
- **Intrinsic locks** (`synchronized`): acquired on entry to a synchronized block or method; released on exit; mutually exclusive; *reentrant* (a thread can re-acquire a lock it already holds, preventing self-deadlock when calling synchronized methods that call other synchronized methods).
- **Lock guarding discipline**: every shared mutable variable must be guarded by exactly one lock; all variables in a compound invariant must be guarded by the same lock.
- **Visibility and reordering**: the JVM memory model allows the compiler and CPU to reorder memory operations. A reading thread may observe stale values or writes in a different order than they were issued. Locking prevents reordering at synchronization boundaries.
- **Volatile variables**: guarantee visibility (no stale reads) but not atomicity. Correct use requires: writes don't depend on the current value; the variable doesn't participate in invariants with other variables; no other reason for locking.

## Connections

- [[wiki/concepts/thread-safety]] — the book's foundational concept; informs the entire chapter structure.
- [[wiki/concepts/atomicity]] — connects to DDIA's lost update problem and compare-and-set; different abstraction level, same underlying issue.
- [[wiki/concepts/locking]] — intrinsic locks, reentrancy, the lock-guarding discipline. Analogous to 2PL in [[wiki/concepts/isolation-levels]].
- [[wiki/concepts/memory-visibility]] — the less obvious half of synchronization; volatile variables. Parallels DDIA's replication visibility anomalies at a different stack layer.
- [[wiki/concepts/isolation-levels]] — DDIA's 2PL is the database-level equivalent of JCIP's intrinsic locking. Lost updates in DDIA correspond to the `++count` race in JCIP.
- [[wiki/concepts/transactions-acid]] — ACID atomicity (all-or-nothing for a group of operations) vs. program-level atomicity (indivisible operations) — related abstractions at different layers.

## Open questions

- The notes only cover Fundamentals. What do later chapters say about higher-level concurrency constructs (`java.util.concurrent`, `BlockingQueue`, `CountDownLatch`, `Semaphore`)? How do they relate to the lock discipline?
- How does the JVM memory model compare to C++11's memory model? Are the visibility and reordering guarantees the same?
- How does Kotlin's coroutine model interact with JCIP's lock discipline — do coroutines eliminate the visibility problem, or just shift it?
- The notes don't mention `happens-before` relationships explicitly — is this covered later in the book? This connects directly to DDIA's treatment of causal ordering.
