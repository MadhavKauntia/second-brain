---
title: Partitioning (Sharding)
type: concept
created: 2026-04-11
updated: 2026-04-11
notes: [Notes/Engineering/designing-data-intensive-applications.md]
related: [Concepts/replication, Concepts/storage-indexes, Concepts/distributed-faults, Topics/distributed-systems, Topics/databases, Sources/designing-data-intensive-applications]
---

# Partitioning (Sharding)

## What it is

Partitioning (also called sharding) means splitting a large dataset across multiple nodes so that each node holds only a subset of the data. The goal is to spread data and query load evenly. An uneven split — where some partitions have disproportionately high load — produces *hot spots* and a *skewed* partition scheme.

### Partitioning strategies for key-value data

**Key-range partitioning:** assign each partition a contiguous range of keys. Range scans are efficient. Risk: if keys are timestamps and writes always go to "today's" partition, that partition becomes a hot spot.

**Hash-of-key partitioning:** apply a hash function to the key; partition by hash range. Distributes writes evenly; eliminates hot spots from non-uniform key distribution. Cost: range queries are no longer efficient — keys that were adjacent are now scattered.

Cassandra's compound key is a pragmatic hybrid: hash the first component of the key for partitioning, use subsequent components as a sorted index within the partition, enabling range queries within a partition but not across partitions.

**Skewed workloads:** even hashing can't solve all hot spots. If millions of requests all use the same key (e.g., a celebrity's profile), they all hash to the same partition. Application-level workarounds (e.g., appending a random suffix to hot keys and writing to multiple partitions) are needed — with the trade-off that reads must query all variants and merge results.

### Secondary indexes and partitioning

**Document-partitioned (local) index:** each partition maintains its own secondary index covering only its own documents. Writes are local; reads require scatter/gather across all partitions and then merge.

**Term-partitioned (global) index:** a global secondary index is itself partitioned, but by the indexed term rather than the primary key. Reads are efficient — query only the partition for the term. Writes are more complex — every write may need to update index partitions spread across multiple nodes.

### Rebalancing partitions

When nodes are added or removed, partitions must be redistributed. Approaches:
- **Hash mod N** — naive; moves too much data when N changes. Not used in practice.
- **Fixed number of partitions** — create far more partitions than nodes; assign several to each node. Adding a node steals some from each existing node. Used by Elasticsearch, Riak.
- **Dynamic partitioning** — split large partitions; merge small ones. Adapts to data size naturally. Used by HBase, MongoDB.
- **Proportional to node count** — fixed partitions per node; total partitions grows with nodes. Used by Cassandra.

### Request routing

Three approaches: route to any node (forward if wrong); routing tier (knows the partition map); client-side awareness (clients know which partition is on which node). ZooKeeper or gossip protocols track the partition → node mapping.

## Why it matters

Partitioning is the mechanism that makes a database horizontally scalable — it is how you spread load beyond what a single machine can handle. But it introduces a new category of problems: routing requests to the right partition, handling secondary indexes that don't align with the primary partition key, and rebalancing as the cluster grows or shrinks. These costs are not trivial and must be weighed against the benefits.

## Evidence & examples

From [[Sources/designing-data-intensive-applications]]:

- Timestamp as partition key creates a temporal hot spot: all current writes go to one partition, all historical reads are spread across others
- Cassandra's compound key design is a concrete example of accepting hash partitioning's trade-offs while recovering range query ability within partitions
- Scatter/gather for secondary index reads can be expensive: if a secondary index query must hit 10 partitions, latency is dominated by the slowest partition (tail latency amplification)

## Tensions & counterarguments

- Partitioning by hash prevents range queries; partitioning by key range prevents even distribution. Neither is universally better — choose based on access patterns.
- Global indexes (term-partitioned) improve read efficiency but make writes slower and more complex, requiring distributed transactions or accepting eventual consistency in the index.
- Dynamic partitioning is elegant in theory but difficult operationally: split storms (many simultaneous splits) can overwhelm a cluster.

## Related

- [[Concepts/replication]] — partitioning is almost always combined with replication for fault tolerance
- [[Concepts/storage-indexes]] — secondary indexes behave differently depending on the partitioning scheme
- [[Concepts/distributed-faults]] — what happens to partitioning when nodes fail
- [[Topics/distributed-systems]] — broader distributed systems context
