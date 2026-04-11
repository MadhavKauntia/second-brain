---
title: "System Design Masterclass — Lecture 01: Foundational Topics"
type: source
created: 2026-04-11
updated: 2026-04-11
notes: "Notes/Engineering/System Design Masterclass/01-foundational-topics-in-system-design.md"
related: [Concepts/scaling, Concepts/delegation, Concepts/communication-protocols, Topics/system-design]
---

# System Design Masterclass — Lecture 01: Foundational Topics

**Type:** course
**Domain:** Engineering
**Ingested:** 2026-04-11
**Notes:** [[Notes/Engineering/System Design Masterclass/01-foundational-topics-in-system-design|My notes from lecture]]

## Summary

The first lecture establishes two things: a general approach to system design, and a worked case study of an online/offline indicator. The design approach centers on identifying the "core" of the system first — the must-have component around which everything else is built — then iterating from a day-0 architecture outward as scale demands change.

The online/offline indicator is a compact illustration of how requirements drive storage choices. A heartbeat push model (clients POST to `/heartbeat` every 10 seconds) is simple and cheap; adding TTL expiry means only active users occupy storage, collapsing what would be a full 1B-user table down to just the online subset. The trade-off is sharp: introducing a "last seen" requirement forces removal of TTL, restoring the full dataset. Requirements and storage architecture are tightly coupled.

The second half surveys six foundational topics — database, caching, scaling, delegation, concurrency, communication — at an introductory level. Each will be explored in depth in later lectures; here they function as an orientation map.

## Key ideas

- **Core-first design** — identify the must-have component before designing around it; the core is use-case specific (could be a DB, a protocol, or a service)
- **Day-0 architecture** — start with the simplest thing that works, then re-architect as each scale threshold is hit
- **Heartbeat pattern** — push-based liveness: clients periodically signal presence; absence after a timeout implies offline
- **TTL-based storage optimization** — expiry collapses storage from all users to active users only; requirements can invalidate this
- **Scale bottom-up** — stateful components (DB, cache) are the real bottleneck; always scale them before stateless API servers
- **Delegation principle** — what doesn't need to happen in real-time shouldn't; async by default for anything eventual
- **Communication patterns** — short polling, long polling, WebSocket (bidirectional), SSE (server-to-client unidirectional)

## Connections

- [[Concepts/scaling]] — vertical vs. horizontal scaling; the stateful bottleneck argument; bottom-up scaling heuristic
- [[Concepts/delegation]] — the async-by-default principle; the kinds of work suited for delegation
- [[Concepts/communication-protocols]] — polling variants, WebSocket, SSE; when each applies
- [[Concepts/locking]] and [[Concepts/atomicity]] — the concurrency section is surface-level but references the same primitives covered in depth in JCIP
- [[Concepts/eventual-consistency]] — the delegation principle mirrors DDIA's framing: not all operations need to be synchronous
- [[Topics/system-design]] — this lecture is the entry point for the system design domain

## Open questions

- When does core-first design break down? Are there systems where no single component is the natural core?
- The heartbeat interval (10s) and offline threshold (30s) are stated as given — what drives those numbers in practice?
- Caching is noted as "not only RAM-based" — what are the other forms, and when do they matter?
- What are the deeper mechanics behind horizontal scaling limits for stateful components?
