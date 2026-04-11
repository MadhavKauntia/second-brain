# Wiki Log

*Append-only. Never edit old entries. Format: `## [YYYY-MM-DD] <operation> | <description>`*

---

## [YYYY-MM-DD] init | Wiki scaffolded

- Pages created: index.md, log.md, overview.md
- Raw sources carried over: Engineering/ (2 book notes)
- Notes: Fresh start. Engineering folder is the first ingest target.

## [2026-04-11] audit | Wikilink format migration to full paths

- Pages updated: all 19 wiki pages (2 sources, 14 concepts, 3 topics, index.md, overview.md)
- Notes: Migrated all wikilinks from short form ([[concepts/slug]], [[sources/slug]], [[topics/slug]]) to full path form ([[wiki/concepts/slug]], etc.) in body text and frontmatter related: fields. Updated index.md entries to [[wiki/type/slug|Title]] format per CLAUDE.md. Also updated overview.md frontmatter. Verified zero violations remain via grep.

## [2026-04-11] ingest | Java Concurrency in Practice (Goetz et al.)

- Pages created: sources/java-concurrency-in-practice, concepts/thread-safety, concepts/atomicity, concepts/locking, concepts/memory-visibility, topics/concurrent-programming
- Pages updated: index.md, concepts/isolation-levels (added locking/atomicity cross-refs), concepts/transactions-acid (added atomicity cross-ref)
- Notes: 4 new concept pages covering JVM concurrency fundamentals. Strong cross-wiki connections: locking ↔ 2PL, atomicity ↔ lost updates, memory-visibility ↔ eventual-consistency replication lag. Notes cover Fundamentals chapter only; open edges around happens-before, j.u.c., and coroutines.

## [2026-04-11] ingest | Designing Data-Intensive Applications (Kleppmann)

- Pages created: sources/designing-data-intensive-applications, concepts/reliability-scalability-maintainability, concepts/data-models, concepts/storage-indexes, concepts/replication, concepts/eventual-consistency, concepts/partitioning, concepts/transactions-acid, concepts/isolation-levels, concepts/distributed-faults, concepts/linearizability, topics/distributed-systems, topics/databases
- Pages updated: index.md, overview.md, log.md
- Notes: First full ingest. 10 concept pages, 2 topic pages, 1 source page. Open threads: consensus algorithms, event sourcing, HTAP, Byzantine fault tolerance in practice.
