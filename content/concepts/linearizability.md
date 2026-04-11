---
title: Linearizability and CAP Theorem
type: concept
created: 2026-04-11
updated: 2026-04-11
notes: [notes/Engineering/designing-data-intensive-applications.md]
related: [concepts/replication, concepts/eventual-consistency, concepts/distributed-faults, topics/distributed-systems, sources/designing-data-intensive-applications]
---

# Linearizability and CAP Theorem

## What it is

**Linearizability** (also called atomic consistency or strong consistency) is the strongest single-object consistency guarantee for a distributed system. A linearizable system behaves as if there is a single copy of the data — even though there are multiple replicas. Specifically: once a write completes, all subsequent reads (from any client, any node) must return the written value or a later one. This is a *recency guarantee*: reads never return stale data.

More precisely: in a linearizable system, every operation appears to take effect instantaneously at some point between its start and completion. All operations can be arranged into a single total order consistent with real time.

### What linearizability requires

A system that reads from any replica without synchronization is generally not linearizable. Specific cases:
- **Single-leader (reads from leader)**: potentially linearizable, if you can guarantee you're reading from the actual current leader (not a deposed leader who doesn't know it's been replaced)
- **Consensus algorithms** (Raft, Paxos): linearizable by design — they prevent split-brain and stale replicas
- **Multi-leader replication**: not linearizable — concurrent writes on multiple nodes with asynchronous merging means no single total order
- **Leaderless replication**: probably not linearizable, even with quorums — timing windows allow a read quorum to include only stale replicas

### CAP Theorem

The CAP theorem states that a distributed data store can guarantee at most two of: **Consistency** (linearizability), **Availability** (every request receives a response), **Partition tolerance** (the system continues operating despite network partitions).

The practical framing: network partitions *will* happen. So the real choice is between **CP** (consistent but unavailable during partitions) and **AP** (available but potentially returning stale/inconsistent data during partitions).

A linearizable system, when a network partition occurs, must choose: either refuse requests (become unavailable) to preserve consistency, or accept requests on isolated replicas (become non-linearizable). This is not a design flaw — it is a mathematical consequence of linearizability's recency guarantee.

### Applications that require linearizability

- **Leader election and distributed locks**: if multiple nodes might believe they are leader simultaneously (split brain), data can be corrupted. The lock must be linearizable.
- **Uniqueness constraints**: enforcing that a username is unique requires a single authoritative point — any two writes happening "simultaneously" must have a defined order.
- **Cross-channel coordination**: if a system writes to a file store and sends a notification via a message queue, a reader who receives the notification and reads the file store must see the update. Without linearizability, they may not.

## Why it matters

Linearizability is often what people intuitively expect when they think about a database, but few distributed databases actually provide it by default — and those that do pay a measurable performance and availability cost. Understanding linearizability makes the trade-off concrete: you are not just choosing "strong consistency" as a feature toggle; you are accepting that during a network partition, your system will become unavailable rather than serve stale data.

The mismatch between expectation (implicit linearizability) and reality (eventual consistency) is a source of subtle correctness bugs in distributed applications. Being explicit about whether linearizability is required — and for which operations — is good engineering practice.

## Evidence & examples

From [[sources/designing-data-intensive-applications]]:

- Multi-leader replication is definitively non-linearizable: it explicitly allows concurrent writes on multiple nodes
- Leaderless quorums appear to promise strong consistency but can be violated: if w + r > n but a write is not yet propagated to all w nodes when a read starts, a read quorum may miss the latest write
- CAP theorem popularized the linearizability-availability trade-off, though the theorem itself is sometimes criticized for being too simplistic (it treats partitions as binary and doesn't account for network delays outside partitions)

## Tensions & counterarguments

- **CAP is a blunt instrument.** Network partitions are rare in practice; the more common trade-off is between consistency and latency, not consistency and availability. The PACELC model extends CAP to cover the latency trade-off under normal operation.
- **Linearizability is sometimes overkill.** Many applications only need causally consistent operations (happens-before relationships), not a global total order. Causal consistency is achievable without the full cost of linearizability and remains available during partitions.
- **"Eventual consistency is fine for most things"** is often stated but rarely carefully validated. Before accepting eventual consistency, enumerate which anomalies are acceptable. See [[concepts/eventual-consistency]].

## Related

- [[concepts/replication]] — which replication strategies can be linearizable
- [[concepts/eventual-consistency]] — the weaker consistency alternative
- [[concepts/distributed-faults]] — the fault conditions under which linearizability is maintained or lost
- [[topics/distributed-systems]] — broader distributed systems theory
