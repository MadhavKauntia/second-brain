---
title: Overview
type: overview
created: 2026-04-11
updated: 2026-04-17
notes: [Notes/Engineering/designing-data-intensive-applications.md, Notes/Engineering/java-concurrency-in-practice.md, Notes/Psychology/dopamine.md, Notes/Engineering/Podcasts/steve-yagge-pragmatic-engineer.md]
related: [Topics/distributed-systems, Topics/databases, Topics/concurrent-programming, Topics/psychology, Topics/ai-engineering]
---

# Overview

*The wiki maintains this page. It reflects the current best synthesis across everything in the wiki. Updated whenever a new source meaningfully shifts the picture.*

---

## Current thesis

Knowledge compounds when the same structural patterns appear across domains. So far: distributed data systems (DDIA), JVM concurrency (JCIP), dopamine neuroscience (Huberman), and AI engineering practice (Yegge) all converge on two recurring tensions — **relative state vs. absolute state**, and **shifted costs vs. eliminated costs**. The mechanisms differ entirely; the underlying logic is the same.

The AI engineering data now sharpens the second tension. The [[Concepts/dracula-effect]] is a concrete case of shifted costs: AI does not eliminate cognitive effort, it shifts it — from routine tasks (eliminated) to judgment and review (intensified). The same pattern holds in distributed systems (replication shifts consistency burden to the application), in JVM concurrency (weak memory models shift visibility guarantees to the programmer), and in dopamine (reward stacking shifts the baseline, not the ceiling). **Every layer of the stack trades one type of cost for another; none actually removes it.**

## Strongest ideas so far

- **The consistency spectrum is concrete.** Both in distributed replication (eventual → linearizable) and in local transactions (read committed → snapshot → serializable), the spectrum represents specific, enumerable anomalies — not vague notions of "correctness." Knowing which anomalies an application can tolerate is more useful than knowing the label of the consistency model.
- **"Schemaless" is a myth.** Document databases don't eliminate schemas; they shift schema enforcement from the database into application code (schema-on-read). This is sometimes a good trade-off, but it is never "schema-free."
- **Storage engine design predetermines the write/read profile.** LSM-trees optimize for write throughput with sequential I/O and background compaction; B-trees optimize for predictable read performance with in-place updates. Analytical (OLAP) workloads benefit from columnar storage entirely distinct from both.
- **CAP theorem is a consequence of linearizability's cost.** A linearizable system must sacrifice availability during network partitions. This is not a design choice but a mathematical constraint of recency guarantees in an asynchronous network.
- **Atomicity and visibility are the same problems at every layer of the stack.** JCIP's read-modify-write race (program level) and DDIA's lost update (database level) are structurally identical. JCIP's memory visibility failures (stale CPU-cached reads) and DDIA's replication lag anomalies (stale replica reads) are structurally identical. The mechanisms differ (locks vs. MVCC, volatile vs. quorum reads) but the underlying concerns run from the CPU cache all the way up to geographically distributed replicas.
- **Relative state determines experience, not absolute state.** Dopamine experience is set by the peak-to-baseline ratio, not the absolute peak level — exactly as DDIA shows that what matters for consistency is the gap between expected and actual data freshness, not the absolute freshness. In both cases, raising the "floor" (dopamine baseline; staleness expectations) without changing the "ceiling" yields no improvement. The pattern extends to engineering teams: absolute performance metrics matter less than performance relative to established expectations.

## Active tensions

- **Weak isolation vs. application complexity.** Most production databases use snapshot isolation by default, not full serializability. This pushes the burden of handling lost updates and write skew onto the application. Many teams don't know this trade-off is being made.
- **Multi-leader replication enables write availability but makes consistency hard.** There is no principled conflict resolution strategy that is both correct and simple.
- **Motivation maintenance vs. optimization.** The dopamine framework argues against consistent reward stacking — but most performance-optimization practices (pre-workout, music, focused conditions) are exactly that. The tension between peak performance in a session vs. long-term baseline preservation is unresolved.

## Open edges

- **Consensus algorithms** (Raft, Paxos) — linearizable systems require them, but this wiki doesn't yet cover how they work
- **Event sourcing and CQRS** — how the "log as truth" pattern interacts with encoding/evolution and storage principles
- **Higher-level JVM concurrency** — `java.util.concurrent`, `happens-before`, lock-free structures, Kotlin coroutines
- **Sleep and dopamine** — how sleep architecture interacts with baseline recovery; a natural next source
- **Intrinsic motivation theory** — where does Deci & Ryan's self-determination theory align with or diverge from the neurochemical model?
- **Habit formation** — dopamine implies habits encode expected reward levels; connections to Duhigg, Clear
- **Stress physiology** — cortisol / dopamine interaction; the other half of the motivation picture
- **AI agent workflows at levels 5–8** — what systematic treatment of multi-agent orchestration looks like; no empirical documentation in the wiki yet
- **Monolith modularisation for AI adoption** — structural path from large-monolith to AI-agent-capable codebase; connects system design to AI engineering

---

*Last updated: 2026-04-17*
