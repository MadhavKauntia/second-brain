---
title: Data Models
type: concept
created: 2026-04-11
updated: 2026-04-11
notes: [notes/Engineering/designing-data-intensive-applications.md]
related: [concepts/storage-indexes, concepts/partitioning, topics/databases, sources/designing-data-intensive-applications]
---

# Data Models

## What it is

A data model organizes data elements and defines how they relate to one another. The three dominant models in use are:

**Relational model** — data is organized into tables (relations) of rows and columns. Relationships between entities are expressed via foreign keys and joins. Roots in business data processing; SQL is declarative.

**Document model** — data is stored as self-contained documents (JSON/BSON/XML), which can contain nested structures. Well-suited for hierarchical, one-to-many data where the entire tree is typically loaded at once. Used by MongoDB, CouchDB, etc. The "schemaless" label is misleading: document databases have an *implicit* schema enforced by application code at read time, not by the database. This is called schema-on-read vs. the relational schema-on-write.

**Graph model** — data is stored as vertices (nodes) and edges (relationships), each with arbitrary key-value properties. Well-suited for highly interconnected data with many-to-many relationships. Queried with traversal languages (Cypher, SPARQL). The property graph model and triple-store model are largely equivalent in expressive power.

**Query language dimension:** SQL is declarative — it specifies *what* you want, not *how* to get it. The database optimizer chooses the execution strategy. This makes SQL amenable to parallelization and query optimization in ways that imperative code is not. The same principle applies beyond databases: CSS is declarative; JavaScript DOM manipulation is imperative.

## Why it matters

Data model choice shapes application code complexity more than almost any other architectural decision. The right model for the data access patterns can yield simpler queries and better performance; the wrong model forces awkward workarounds.

The *impedance mismatch* problem — the gap between object-oriented application code and relational tables — is a persistent source of accidental complexity. ORMs help but can introduce subtle bugs when they silently use unsafe read-modify-write cycles instead of atomic database operations.

## Evidence & examples

From [[sources/designing-data-intensive-applications]]:

- Document model reduces impedance mismatch for hierarchical data, but struggles with joins and many-to-many relationships
- Graph model is essentially a generalization: if you have many-to-many relationships everywhere, model it as a graph rather than fighting the relational join machinery
- Property graph can be represented *in* a relational schema (vertices and edges as tables), demonstrating that models can be translated but not always efficiently
- Cassandra's compound primary key is a pragmatic hybrid: hash the first part for partitioning, sort on subsequent parts for range queries within a partition

## Tensions & counterarguments

- **Document vs. relational isn't a permanent choice.** Relational databases have added JSON column types; document databases have added join-like operations. The boundaries are blurring.
- **"Schemaless" as a selling point** conflates flexibility with rigor. Schema-on-read pushes enforcement into application code, where it's harder to test and easier to miss. Schema migrations in relational databases are painful but force discipline.
- **Graph databases vs. recursive relational queries:** Graph traversal in a relational database is possible (recursive CTEs) but awkward — the number of joins isn't known in advance. Graph databases make variable-depth traversal natural.

## Related

- [[concepts/storage-indexes]] — how data models are physically implemented on disk
- [[concepts/partitioning]] — how document and key-value models are distributed
- [[topics/databases]] — broader database engineering context
