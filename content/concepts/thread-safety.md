---
title: Thread Safety
type: concept
created: 2026-04-11
updated: 2026-04-11
raw: [Engineering/java-concurrency-in-practice.md]
related: [concepts/atomicity, concepts/locking, concepts/memory-visibility, topics/concurrent-programming, sources/java-concurrency-in-practice]
---

# Thread Safety

## What it is

A class is *thread-safe* if it behaves correctly when accessed from multiple threads, regardless of the scheduling or interleaving of the execution of those threads by the runtime environment, and with no additional synchronization required from calling code.

The key reframe: **thread safety is about managing access to shared, mutable state — not about threads.** A variable is *shared* if it could be accessed by multiple threads. It is *mutable* if its value could change during its lifetime. Whenever more than one thread accesses a given state variable, and at least one of them might write to it, all of them must coordinate their access using synchronization.

Three strategies guarantee thread safety, in order of preference:

1. **Don't share state across threads.** If a variable is local to a thread's stack (e.g., a local variable inside a method), it is inaccessible to other threads and needs no synchronization. Stateless objects — those with no fields and no references to fields of other classes — are *always* thread-safe regardless of how many threads use them concurrently.

2. **Make the state variable immutable.** An object whose state cannot change after construction requires no synchronization. Immutability eliminates the problem at its root.

3. **Synchronize all access consistently.** When sharing mutable state is necessary, every access (both reads and writes) must be protected by the same synchronization mechanism.

## Why it matters

Thread safety bugs are among the hardest to find and reproduce. They are typically triggered only by specific scheduling interleavings that may be rare under normal load and nearly impossible to reproduce in testing. A class that is not thread-safe may work correctly for years and then fail in production under high concurrency or on a different JVM implementation.

The three-strategy framework is useful because it clarifies that synchronization (strategy 3) should be a last resort. The safest concurrent code is code that avoids sharing state entirely. The second safest is immutable state. Synchronization is necessary when neither of the first two strategies is practical.

## Evidence & examples

From [[sources/java-concurrency-in-practice]]:

- `StatelessFactorizer`: a servlet with no fields and no references to other classes' fields. It is unconditionally thread-safe regardless of how many threads call it simultaneously — there is nothing to share.
- `UnsafeCountingFactorizer`: adds a `long count` field. The `++count` operation appears simple but is a read-modify-write sequence — not atomic. Two threads can both read `9`, both compute `10`, and both write `10`, silently losing one increment.

## Tensions & counterarguments

- **Immutability is not always achievable.** Many real-world objects must hold mutable state. The strategy hierarchy acknowledges this — immutability is preferred where possible, not mandated everywhere.
- **"No additional synchronization from calling code"** is a strict requirement. Many classes are documented as *not* thread-safe (e.g., `ArrayList`, `HashMap`) and are safe only when used within a single thread or when the caller provides external synchronization. Conflating "I'm using it carefully" with "it is thread-safe" is a source of bugs.
- **Performance vs. safety**: excessive synchronization can serialize operations that could run concurrently, degrading performance. Identifying the minimal set of state that must be synchronized — and choosing the right granularity — is the real engineering challenge.

## Related

- [[concepts/atomicity]] — operations that must execute as an indivisible unit to preserve thread-safe behavior
- [[concepts/locking]] — the primary synchronization mechanism for shared mutable state
- [[concepts/memory-visibility]] — the often-overlooked second half of synchronization
- [[topics/concurrent-programming]] — broader context for concurrent program design
