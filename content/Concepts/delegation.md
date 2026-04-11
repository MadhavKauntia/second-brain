---
title: Delegation
type: concept
created: 2026-04-11
updated: 2026-04-11
notes: []
related: [Concepts/scaling, Concepts/eventual-consistency, Topics/system-design, Sources/system-design-masterclass-01]
---

# Delegation

## What it is

Delegation is the design principle that work which does not need to happen in real-time should not happen in real-time. The core pattern: **receive a request, delegate the work, respond immediately**. Actual processing happens asynchronously, decoupled from the client interaction.

Work suited for delegation: long-running tasks (spinning up infrastructure), heavy computation queries, batch writes, and anything whose result the client does not need synchronously.

## Why it matters

Synchronous processing ties up request-handling resources for the full duration of the work. Delegating expensive operations to a background queue or worker reduces latency on the critical path and decouples request rate from processing rate — a spike in incoming requests can be absorbed by the queue rather than dropped or backed up.

The principle is also a requirements-clarification tool. "Does this actually need to be synchronous?" surfaces assumptions. Many operations that appear to require immediate results can tolerate eventual completion.

## Evidence & examples

From [[Sources/system-design-masterclass-01]]: "what does not need to be done in realtime should not be done in realtime." Examples given: spinning up EC2 instances, heavy computation, batch writes — all operations where the user does not need the result before the next interaction.

This maps directly to [[Concepts/eventual-consistency]] from DDIA — the same trade-off applied at the storage layer. Eventual consistency accepts that replicas may lag; delegation accepts that results may arrive later. Both sacrifice immediacy for throughput and availability.

## Tensions & counterarguments

- Delegation introduces asynchrony, which adds complexity: job queues, retry logic, idempotency, and result delivery all become concerns.
- "Anything that could be eventual" requires actually knowing what the user needs synchronously — which requires real requirements analysis, not guessing.

## Related

- [[Concepts/scaling]] — delegation reduces synchronous demand, complementing capacity scaling
- [[Concepts/eventual-consistency]] — DDIA's storage-layer analog of the same trade-off
- [[Topics/system-design]]
