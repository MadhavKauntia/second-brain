---
title: Storage Engines and Indexes
type: concept
created: 2026-04-11
updated: 2026-04-11
notes: [notes/Engineering/designing-data-intensive-applications.md]
related: [concepts/data-models, concepts/transactions-acid, concepts/replication, topics/databases, sources/designing-data-intensive-applications]
---

# Storage Engines and Indexes

## What it is

A storage engine is the component of a database that physically stores and retrieves data. The two dominant families are log-structured (LSM-tree-based) and page-oriented (B-tree-based). Indexes are additional data structures derived from primary data that speed up reads at the cost of write overhead.

### Log-Structured (LSM-Tree) family

**Hash indexes**: An in-memory hash map maps each key to its byte offset in an append-only log. Fast writes (sequential append); reads require a hash lookup then one seek. Compaction runs in the background to merge segments and discard duplicate/deleted keys. Limited to key-value data that fits in memory.

**SSTables (Sorted String Tables)**: Segment files where keys are sorted. Sorting enables efficient merging (mergesort) and allows a sparse in-memory index — you only need offsets for some keys; for others, you scan from the nearest known offset. An in-memory balanced tree (red-black, AVL) called a *memtable* buffers writes; when it reaches a threshold, it's flushed to disk as an SSTable.

**LSM-Trees (Log-Structured Merge Trees)**: The full system built on SSTables: writes go to the memtable → periodic flush to disk → background compaction merges segments. Used by LevelDB, RocksDB, Cassandra, HBase, and others. Key characteristic: writes are sequential (fast on HDD/SSD); reads may need to check multiple levels (mitigated by Bloom filters).

### Page-Oriented (B-Tree) family

B-trees organize data into fixed-size pages (typically 4KB), read and written one page at a time. One page is the root; pages contain keys and references to child pages; leaf pages contain the values (or references to them). The branching factor (typically hundreds) keeps tree height low (3–4 levels for a database with millions of keys).

Writes update pages in-place, which requires a **write-ahead log (WAL)** for crash recovery — every modification is written to the WAL before being applied to the tree. Concurrent access requires latches (lightweight locks) on tree internals.

### Clustered vs. heap indexes

A **heap file** stores actual row data separately from the index. The index just holds a reference to the heap location. Multiple secondary indexes all point to the same heap. A **clustered index** stores the row data directly in the index (the primary key index in InnoDB, for example). Faster reads (no heap hop) but more complex writes.

### OLTP vs. OLAP; Column-Oriented Storage

OLTP (Online Transaction Processing): many small, user-facing reads and writes. Row-oriented storage is natural — you typically want all fields for a given row.

OLAP (Online Analytical Processing): few large queries scanning millions of rows, typically computing aggregates over a few columns. **Column-oriented storage** stores each column in a separate file. A query that touches 5 of 100 columns only reads 5 files. Columns are also highly compressible (repeated values) — bitmap encoding is particularly effective. The downside is slower writes.

Data warehouses use a **star schema**: a central fact table connected to dimension tables. ETL (Extract-Transform-Load) pipelines move data from OLTP systems into the warehouse.

## Why it matters

Storage engine choice has direct performance consequences:

- **LSM-trees** offer higher write throughput and better compression but unpredictable read latency (compaction can interfere) and multiple copies of the same key across segments
- **B-trees** offer more predictable read performance and each key appears exactly once (easier for transactions) but are slower on writes and less space-efficient

For analytical workloads, using a row-oriented OLTP database for queries that scan most rows is a category error — column-oriented storage exists precisely to avoid reading data that isn't needed.

## Evidence & examples

From [[sources/designing-data-intensive-applications]]:

- The simplest database (two bash functions) illustrates why indexes exist — `db_get()` with a linear scan is O(n); an index reduces it to O(log n) or O(1)
- B-tree branching factor of a few hundred means a tree of depth 4 can address 256TB of data
- Compaction in LSM-trees can be a performance problem in production: if write throughput is high and compaction can't keep up, read performance degrades and disk usage grows

## Tensions & counterarguments

- The LSM vs. B-tree choice is not absolute — some systems use hybrid approaches, and benchmarks vary widely depending on workload (write-heavy vs. read-heavy, random vs. sequential).
- Column-oriented storage has write amplification issues — a single row write must update N column files. Solutions include writing to an in-memory row store and periodically merging into the columnar format (used by Vertica, DuckDB).
- "Column-oriented storage" in document databases (like Parquet files, or BigQuery) is conceptually different from columnar OLAP databases — the implementation details differ significantly.

## Related

- [[concepts/data-models]] — the logical layer above physical storage
- [[concepts/transactions-acid]] — B-tree WAL is part of durability; isolation requires careful coordination with storage
- [[concepts/replication]] — replication logs (WAL shipping, logical logs) are built on top of the storage layer
- [[topics/databases]] — broader database engineering context
