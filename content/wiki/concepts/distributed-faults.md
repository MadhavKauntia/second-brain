---
title: Distributed Systems Faults
type: concept
created: 2026-04-11
updated: 2026-04-11
sources: [Engineering/designing-data-intensive-applications.md]
related: [wiki/concepts/replication, wiki/concepts/linearizability, wiki/concepts/reliability-scalability-maintainability, wiki/topics/distributed-systems, wiki/sources/designing-data-intensive-applications]
---

# Distributed Systems Faults

## What it is

In a distributed system, partial failures are unavoidable: some components may fail while others continue operating, and failures are *nondeterministic* — the same operation may succeed sometimes and fail unpredictably at other times. This is categorically different from single-machine systems where faults are typically total (the machine fails) and deterministic (you can usually reproduce them).

### Unreliable Networks

Distributed nodes communicate over asynchronous packet networks. A message may be lost, delayed, reordered, or arrive multiple times. When a timeout occurs, you cannot tell if the remote node received your request — the node may have processed it and only the response was lost.

Handling network faults doesn't mean tolerating them silently — it means ensuring your system degrades gracefully and can recover. Key insight: **you cannot reliably detect remote failures over an asynchronous network.** Timeouts are a heuristic, not a fact. Declaring a node dead too early can cause unnecessary leader elections and extra load; declaring it dead too late extends the outage.

### Unreliable Clocks

Each node has its own clock, and clocks drift. NTP synchronization reduces drift but cannot eliminate it. Two specific clock types:
- **Time-of-day clocks**: useful for human time (timestamps, TTLs), but can jump backward (NTP reset) and have variable accuracy. Not suitable for measuring elapsed time.
- **Monotonic clocks**: suitable for measuring durations (timeouts, performance) — guaranteed to only move forward, but the absolute value is meaningless and not comparable across machines.

**The danger of relying on synchronized clocks:** If two nodes assign timestamps to events and then compare those timestamps to determine "which happened first," a clock ahead by even a few milliseconds can corrupt the ordering. Using last-write-wins by timestamp is therefore dangerous — it silently discards writes that happen to come from a slightly slow clock.

**Process pauses** compound the clock problem. A GC pause of several hundred milliseconds, a VM being migrated, or a OS scheduling interruption can cause a thread to stop executing for an indeterminate time. During the pause, the rest of the world advances — a lease may expire, a new leader may be elected — and when the paused node resumes, it may not know anything happened.

### System Models

To reason about distributed algorithms, their correctness is defined against a *system model* — a set of assumptions about the environment:

**Network models:**
- *Synchronous*: bounded network delay, process pauses, clock error. Not realistic.
- *Partially synchronous*: usually behaves synchronously, but sometimes exceeds bounds. Realistic.
- *Asynchronous*: no timing assumptions at all; no clock. Very constrained but most general.

**Node failure models:**
- *Crash-stop*: a node that fails simply stops; it never does anything again.
- *Crash-recovery*: nodes can fail and restart; stable storage is preserved across crashes, in-memory state is lost.
- *Byzantine*: nodes can behave arbitrarily, including sending malicious or corrupted messages. The hardest model. Relevant in adversarial environments (public blockchains, safety-critical systems) but typically not assumed in datacenter databases.

### Safety and Liveness

Algorithm properties are categorized as:
- **Safety** ("nothing bad happens"): invariants that must hold at every point in time — e.g., "two nodes never simultaneously believe they are leader"
- **Liveness** ("something good eventually happens"): progress guarantees — e.g., "if a correct node requests a value, it eventually receives a response"

In the asynchronous model, safety properties can always be maintained (just stop doing anything), but liveness may require timing assumptions. Most practical distributed algorithms tolerate partial synchrony: they guarantee safety always, and liveness when timing bounds are eventually respected.

## Why it matters

Building correct distributed systems requires internalizing that **suspicion, pessimism, and paranoia are correct engineering attitudes**, not pathology. A node cannot fully trust its own state (it may have been declared dead by a quorum), its clocks (they may be wrong), or the network (requests may be in-flight but not acknowledged).

The practical upshot: systems must be designed to handle every operation being retried, any node being declared dead at any time, and any write arriving in any order. These are not edge cases — they are the normal operating conditions of any large-scale distributed system.

## Evidence & examples

From [[wiki/sources/designing-data-intensive-applications]]:

- The lease-renewal Java code example: a GC pause between `lease.isValid()` and `process(request)` means the node continues processing after the lease expired and another leader was elected — a real correctness bug
- Byzantine Generals Problem: reaching consensus when some nodes may lie — provably unsolvable without 3f+1 nodes to tolerate f Byzantine faults
- Supercomputer vs. internet service comparison: supercomputers checkpoint and reboot on failure; internet services must continue operating with degraded components, not halt

## Tensions & counterarguments

- **Byzantine fault tolerance is expensive** — most datacenter databases assume crash-stop or crash-recovery models. Byzantine tolerance requires >2/3 of nodes to be correct and involves significant communication overhead. Only worth it when the threat model includes adversarial nodes.
- **The asynchronous network model is too pessimistic** for building practical systems. Most real algorithms assume partial synchrony — they need timing assumptions for liveness but not for safety. The challenge is that the partial synchrony assumption is hard to validate and occasionally violated in production.
- **Clocks are better than they used to be** — GPS-disciplined clocks (like in Spanner's TrueTime) provide bounded uncertainty of ~7ms, enabling timestamping with known error bounds. This makes last-write-wins safer but not free.

## Related

- [[wiki/concepts/replication]] — replication protocols must handle all of these failure modes
- [[wiki/concepts/linearizability]] — strong consistency guarantee under these fault conditions
- [[wiki/concepts/reliability-scalability-maintainability]] — distributed faults are the adversary that reliability is designed against
- [[wiki/topics/distributed-systems]] — broader distributed systems theory
