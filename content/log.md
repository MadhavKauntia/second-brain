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

## [2026-04-11] rename | Rename raw/ directory to notes/; update all path references

- Pages updated: all 20 wiki pages (2 sources, 14 concepts, 3 topics, overview.md) plus CLAUDE.md
- Notes: Renamed physical raw/ directory to notes/. Updated raw: frontmatter key to notes: across all pages. Updated notes path values from [Engineering/...] to [notes/Engineering/...]. Updated body links from [[raw/Engineering/...]] to [[notes/Engineering/...]]. Updated **Raw notes:** labels to **Notes:**. Zero raw/ path references remain.

## [2026-04-11] migrate | Move wiki/ subdirectories to content root; update all wikilinks

- Pages created: index.md (replaced homepage with new 4-section catalog)
- Pages updated: all 22 wiki pages (2 sources, 14 concepts, 3 topics, overview.md); wiki/index.md deleted
- Notes: Moved sources/, concepts/, topics/ from wiki/ subdirectory to content root. Updated all wikilinks from [[wiki/X/Y]] to [[X/Y]] throughout body text and frontmatter related: fields. Renamed sources: frontmatter key to raw: across all pages. Added **Raw notes:** link to both source pages. Deleted wiki/index.md and empty wiki/ directory.

## [2026-04-11] ingest | Designing Data-Intensive Applications (Kleppmann)

- Pages created: sources/designing-data-intensive-applications, concepts/reliability-scalability-maintainability, concepts/data-models, concepts/storage-indexes, concepts/replication, concepts/eventual-consistency, concepts/partitioning, concepts/transactions-acid, concepts/isolation-levels, concepts/distributed-faults, concepts/linearizability, topics/distributed-systems, topics/databases
- Pages updated: index.md, overview.md, log.md
- Notes: First full ingest. 10 concept pages, 2 topic pages, 1 source page. Open threads: consensus algorithms, event sourcing, HTAP, Byzantine fault tolerance in practice.
