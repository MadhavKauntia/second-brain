---
title: Replication
type: concept
created: 2026-04-11
updated: 2026-04-11
raw: [Engineering/designing-data-intensive-applications.md]
related: [concepts/eventual-consistency, concepts/linearizability, concepts/distributed-faults, concepts/partitioning, topics/distributed-systems, topics/databases, sources/designing-data-intensive-applications]
---

# Replication

## What it is

Replication means keeping copies of the same data on multiple machines connected via a network. The three primary architectures are single-leader, multi-leader, and leaderless.

### Single-Leader (Leader-Follower)

One node is the leader; all writes go through it. The leader writes to its local storage and sends a replication log to followers. Reads can be served by any replica. If the leader fails, a follower is promoted (**failover**).

Replication can be **synchronous** (leader waits for follower acknowledgment before confirming write to client — strong durability guarantee, but blocks if follower is unavailable) or **asynchronous** (leader confirms immediately — available but durability is not guaranteed if leader fails before replication). In practice, most deployments are asynchronous or "semi-synchronous" (one synchronous follower, rest async).

**Replication log implementations:**
- **Statement-based replication**: replay SQL statements on followers. Fragile — non-deterministic functions (`NOW()`, `RAND()`) produce different results.
- **WAL shipping**: ship the storage engine's write-ahead log. Tightly coupled to storage engine internals; version upgrades are harder.
- **Logical (row-based) log replication**: a higher-level log of row changes. Decoupled from storage engine; can replicate to different storage engine versions. Used by MySQL binlog row format, PostgreSQL logical decoding.
- **Trigger-based replication**: custom application code fires on writes. Flexible but high overhead and prone to bugs.

### Multi-Leader

More than one node accepts writes; each leader also acts as a follower to the others. Write conflicts are inherent and must be resolved.

**Use cases:** multi-datacenter operation (one leader per datacenter), offline clients (each device is its own leader), collaborative editing (each client has a local replica).

**Conflict resolution approaches:** last-write-wins (discards concurrent writes by timestamp — dangerous with unreliable clocks), custom resolution logic (on-write in a background handler, or on-read by presenting conflicts to the application), and CRDT-based merging.

### Leaderless (Dynamo-style)

Any replica can accept writes. Reads and writes are sent to multiple replicas in parallel. **Quorum** conditions determine success: if there are n replicas, a write must succeed on w nodes, and a read must contact r nodes. As long as w + r > n, at least one read node must have the latest write.

**Read repair** and **anti-entropy** processes keep replicas in sync. **Sloppy quorums** allow writes to "home in" on available (but wrong) nodes during network partitions, with **hinted handoff** later returning them to the correct nodes.

**Version vectors** track causal history across replicas to detect and resolve concurrent writes.

## Why it matters

Replication is the primary mechanism for two critical system properties: **fault tolerance** (data survives node failures) and **read scalability** (read load can be distributed across replicas). But replication introduces **consistency trade-offs** — replicas can diverge, and the gap before convergence creates anomalies visible to users. See [[concepts/eventual-consistency]].

Leader failover is deceptively hard. Split-brain (two nodes both believe they are leader) can corrupt data. Failover requires detecting the failure, electing a new leader, reconfiguring clients, and handling any writes that reached the old leader but not its replicas — all under uncertainty.

## Evidence & examples

From [[sources/designing-data-intensive-applications]]:

- Setting up a new follower without downtime: snapshot → copy → catch up from replication log. This requires the log position of the snapshot to be known.
- Fully asynchronous replication can lose committed writes if the leader fails before followers catch up — a durability–availability trade-off
- Multi-leader in multi-datacenter: each datacenter operates independently in case of inter-datacenter network issue; writes are merged asynchronously. Better write latency to local users, but conflicts are possible.
- Leaderless replication is "probably not linearizable" even with quorum settings — subtle timing windows can allow stale reads to satisfy quorum conditions

## Tensions & counterarguments

- Multi-leader avoids single points of failure but introduces write conflicts that are fundamentally hard to resolve correctly. Most applications don't have a conflict resolution strategy that is both correct and efficient.
- Leaderless replication sacrifices ordering guarantees. Without a total ordering of writes, causal consistency requires explicit version tracking (version vectors), which adds overhead.
- Synchronous replication gives strong durability but reduces write availability. Every additional synchronous replica is another potential point of write failure.

## Related

- [[concepts/eventual-consistency]] — replication lag and the anomalies it produces
- [[concepts/linearizability]] — which replication strategies can be made linearizable
- [[concepts/distributed-faults]] — what happens when nodes and networks fail during replication
- [[concepts/partitioning]] — replication and partitioning are typically combined
- [[topics/distributed-systems]] — broader theory of distributed systems
