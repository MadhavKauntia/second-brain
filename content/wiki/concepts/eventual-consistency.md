---
title: Eventual Consistency and Replication Lag
type: concept
created: 2026-04-11
updated: 2026-04-11
sources: [Engineering/designing-data-intensive-applications.md]
related: [wiki/concepts/replication, wiki/concepts/linearizability, wiki/concepts/isolation-levels, wiki/topics/distributed-systems, wiki/sources/designing-data-intensive-applications]
---

# Eventual Consistency and Replication Lag

## What it is

In asynchronous replication, followers may lag behind the leader. During this lag, reads from followers may return stale data. *Eventual consistency* is the guarantee that, if writes stop, all replicas will converge to the same value — but it makes no promise about *when*, and it allows a range of anomalies in the meantime.

Eventual consistency is not one thing — it is a family of weak guarantees. The specific anomalies it allows (and the stronger guarantees that rule them out) are:

**Read-after-write consistency (read-your-own-writes):** A user who submits data should see what they wrote when they next read. Without this, a user may post a comment and then not see it — not because it failed, but because the read went to a lagging replica. Implementing it requires routing reads to the leader for data the user recently modified, or using a timestamp-based replica selection strategy.

**Monotonic reads:** A user should not see data "move backward in time" — i.e., if they see a value at time T, a subsequent read should not return a value from before T. This can happen if consecutive reads are routed to different followers with different lag. Solution: sticky routing — route each user's reads to the same replica consistently.

**Consistent prefix reads:** If a sequence of writes happens in a certain causal order, readers should see them in that order. Example: if Alice writes "How are you?" and Bob writes "I'm fine", a third party should not see "I'm fine" before "How are you?" even if the writes went to different shards. Ensuring causal ordering requires writing causally related operations to the same partition.

## Why it matters

Eventual consistency is often treated as a binary — a system either has it or doesn't. In practice, it is a spectrum of anomalies, and applications need to be aware of which anomalies their consistency model permits. Many bugs in distributed systems are caused by developers assuming stronger guarantees than the system actually provides.

The gap between "eventually consistent" (marketing) and the specific anomalies enumerated above (engineering) is significant. An eventually consistent system can, in principle, serve wildly incorrect data for an arbitrary period. Read-your-own-writes is the minimum most user-facing applications actually need but rarely explicitly guarantee.

## Evidence & examples

From [[wiki/sources/designing-data-intensive-applications]]:

- Facebook's timeline inconsistency example: a user posts, reloads the page, and doesn't see their own post because the read hit a lagging replica
- Monotonic reads violation: a user refreshes a page and sees an older version of the data than what they saw moments ago, as if traveling backward in time
- Consistent prefix reads: conversation messages appearing out of order due to writes going to different shards

## Tensions & counterarguments

- **Stronger guarantees have costs.** Read-after-write consistency increases read load on the leader. Monotonic reads via sticky routing reduces load balancing flexibility. Consistent prefix reads may require cross-partition coordination.
- **Eventual consistency can be acceptable** for many workloads — analytics dashboards, search indexes, recommendation systems. The question is whether the anomalies matter to the application's users.
- The term "eventual consistency" has been diluted by marketing. It is worth being precise: when evaluating a system, ask what specific anomalies are possible, not just whether it is "eventually consistent."

## Related

- [[wiki/concepts/replication]] — the mechanism that produces replication lag
- [[wiki/concepts/linearizability]] — the strong consistency alternative: no stale reads, ever
- [[wiki/concepts/isolation-levels]] — analogous spectrum of guarantees for concurrent local transactions
- [[wiki/topics/distributed-systems]] — broader theory of distributed consistency
