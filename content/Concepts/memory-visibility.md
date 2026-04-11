---
title: Memory Visibility and Reordering
type: concept
created: 2026-04-11
updated: 2026-04-11
notes: [Notes/Engineering/java-concurrency-in-practice.md]
related: [Concepts/thread-safety, Concepts/locking, Concepts/eventual-consistency, Topics/concurrent-programming, Sources/java-concurrency-in-practice]
---

# Memory Visibility and Reordering

## What it is

In a multi-threaded program, there is **no guarantee** that a reading thread will see the value written by another thread on a timely basis — or at all — unless explicit synchronization is used. The JVM memory model allows the compiler and CPU to reorder memory operations for performance. This is separate from mutual exclusion: even if only one thread writes and one thread reads, the reading thread may observe stale values.

**Reordering:** the compiler, JVM, and CPU are all permitted to reorder memory operations, as long as the program behaves correctly *within a single thread*. From another thread's perspective, the order of writes may appear different from the order in the source code.

Example: if thread A writes `number = 42` and then `ready = true`, thread B polling on `ready` might observe `ready == true` while still reading `number == 0`. The write to `number` may not have been flushed to main memory, or the two writes may have been reordered by the compiler.

**Synchronization boundaries:** locking a `synchronized` block forces all pending writes to be flushed to main memory and all subsequent reads to be fetched from main memory, not from CPU caches. This is why locking provides memory visibility, not just mutual exclusion. Reading and writing threads must synchronize on the *same* lock.

**Volatile variables:** declaring a field `volatile` tells the compiler and runtime not to reorder operations on it with other memory operations, and to always read from and write to main memory directly. A write to a volatile variable is immediately visible to all threads.

Volatile is correct *only* when all three conditions hold:
1. Writes to the variable do not depend on its current value (no read-modify-write)
2. The variable does not participate in invariants with other state variables
3. No other reason for locking while the invariant is being accessed

A volatile flag for signaling thread termination (`volatile boolean stopped`) is a canonical correct use. An increment counter is not — `++count` reads the current value and writes a new one, so condition 1 is violated.

## Why it matters

Memory visibility is the less intuitive half of concurrency. Most programmers learn about race conditions — two threads writing to the same variable — but many miss that visibility failures can occur even with a single writer. The `NoVisibility` example in JCIP is striking precisely because the race condition is not obvious: no two threads are modifying the same variable simultaneously, yet the program can loop forever or print the wrong value.

In practice, visibility bugs often go unnoticed in development (single-CPU machines, or JVM implementations that happen to flush caches frequently) and surface in production on multi-CPU hardware. They are notoriously hard to reproduce.

The key insight: **locking is not just about mutual exclusion — it is also about memory visibility.** A class that uses locking to protect write access but allows unsynchronized reads is not thread-safe, because the reading thread may see stale values.

## Evidence & examples

From [[Sources/java-concurrency-in-practice]]:
- `NoVisibility`: two static fields, one thread writes `number = 42; ready = true`, another thread loops until `ready` is true then prints `number`. Can loop forever (never sees `ready = true`) or print `0` (sees `ready = true` before seeing `number = 42`). Both outcomes result from reordering and visibility, not from a write race.

## Tensions & counterarguments

- **Volatile is a footgun.** It looks simpler than locking and is easy to misapply. The three conditions for correct volatile use are easy to violate accidentally. In most cases, a lock or an `AtomicXxx` class is safer even if slightly more overhead.
- **Modern hardware makes visibility bugs rare in practice — but not impossible.** On x86, the memory model is relatively strong; on ARM, it is weaker. A program that "works fine" on an x86 development machine may exhibit visibility bugs on an ARM production server (e.g., Android devices, AWS Graviton instances).
- **The connection to distributed systems:** DDIA's treatment of replication lag and stale reads (see [[Concepts/eventual-consistency]]) is the same visibility problem, scaled to distributed nodes. In both cases, writes are not immediately visible to all readers; in both cases, explicit synchronization or consistency protocols are needed to guarantee recency. The analogy runs surprisingly deep: volatile ≈ read-your-own-writes; synchronized ≈ linearizability.

## Related

- [[Concepts/thread-safety]] — visibility is one of the two properties synchronization must provide
- [[Concepts/locking]] — locking provides visibility as a side effect of synchronization boundaries
- [[Concepts/eventual-consistency]] — the distributed analogue: stale reads due to replication lag mirror stale reads due to cache/reordering
- [[Topics/concurrent-programming]] — broader concurrent programming context
