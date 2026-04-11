---
title: Concurrent Programming
type: topic
created: 2026-04-11
updated: 2026-04-11
raw: [Engineering/java-concurrency-in-practice.md]
related: [concepts/thread-safety, concepts/atomicity, concepts/locking, concepts/memory-visibility, concepts/isolation-levels, topics/distributed-systems]
---

# Concurrent Programming

## What this topic covers

The design and implementation of programs that execute multiple computations simultaneously within a single process — threads, synchronization, memory models, and the correctness guarantees required when shared state is accessed concurrently.

## Core concepts

- [[concepts/thread-safety]] — what it means for a class to be safe under concurrent access; the three strategies (no sharing, immutability, synchronization)
- [[concepts/atomicity]] — operations that must execute as indivisible units; read-modify-write races; program-level vs. database-level atomicity
- [[concepts/locking]] — intrinsic locks, reentrancy, lock-guarding discipline; deadlock; fine vs. coarse granularity
- [[concepts/memory-visibility]] — the JVM memory model; reordering; volatile variables; visibility as a separate concern from mutual exclusion

## Key sources

- [[sources/java-concurrency-in-practice]] — foundational treatment of JVM concurrency; thread safety, locking, and visibility

## Synthesis

Concurrent programming on the JVM is harder than it looks because correctness requires addressing two distinct problems simultaneously: **mutual exclusion** (preventing two threads from observing each other's partial writes) and **memory visibility** (ensuring a reading thread sees the most recent write, not a cached stale value). Most programmers know about mutual exclusion; many miss visibility.

The organizing insight from JCIP: the problem is not threads — it is *shared, mutable state*. The safest concurrent code eliminates sharing (stateless, thread-local) or eliminates mutability (immutable objects). Synchronization is the fallback when neither is possible.

Locking is the primary synchronization tool, but it must be applied with discipline. The lock-guarding discipline — one lock per variable, the same lock for all variables in a compound invariant — is not optional. Locks present but not consistently applied provide a false sense of safety.

**Connection to distributed systems:** The same problems appear at the distributed layer. Program-level atomicity (read-modify-write races, CAS) is the within-process version of DDIA's lost update problem. Memory visibility (stale reads due to CPU cache reordering) is the single-process version of DDIA's replication lag. Locking (2PL in the database) is the database-level version of Java's `synchronized`. The abstractions are different but the underlying concerns — atomicity and visibility under concurrent access — are identical.

## Open edges

- Higher-level concurrency constructs: `java.util.concurrent` (`BlockingQueue`, `CopyOnWriteArrayList`, `CountDownLatch`, `Semaphore`) — how do these relate to the fundamental lock discipline?
- The `happens-before` relationship in the Java memory model — a formal way to reason about visibility that JCIP's notes don't yet cover
- Lock-free data structures and CAS loops — when are they better than locking, and when do they degrade?
- Kotlin coroutines and structured concurrency — do they eliminate the visibility problem, or change its character?
- Actor models (Akka, Erlang) — an approach that avoids shared state entirely by making message-passing the only communication mechanism
