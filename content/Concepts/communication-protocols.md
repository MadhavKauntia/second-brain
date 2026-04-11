---
title: Communication Protocols
type: concept
created: 2026-04-11
updated: 2026-04-11
notes: []
related: [Topics/system-design, Sources/system-design-masterclass-01]
---

# Communication Protocols

## What it is

The patterns governing how clients and servers exchange data over a network. The choice of protocol determines who initiates communication, how long connections stay open, and whether the channel is unidirectional or bidirectional.

**Short polling:** The client sends requests on a fixed interval. Simple, but wasteful — most requests return no new data, and latency is bounded by the polling interval.

**Long polling:** The client opens a request; the server holds it until new data is available, then responds and closes. The client immediately reopens. Reduces wasted round-trips vs. short polling while staying within HTTP request/response semantics.

**WebSocket:** A persistent, bidirectional channel. After an initial HTTP upgrade handshake, either party can send data at any time. Suited for high-frequency, low-latency, bidirectional communication (chat, live collaboration, gaming).

**Server-Sent Events (SSE):** A persistent, server-to-client unidirectional stream over HTTP. The server pushes events; the client cannot send back on the same channel. Simpler than WebSocket when the server is the only sender (live feeds, notifications).

## Why it matters

Protocol choice has direct implications for latency, server resource usage, and scaling complexity. Short polling is easy to implement but creates unnecessary load at scale. WebSocket is powerful but requires stateful connection management — horizontal scaling needs a shared pub/sub layer so any server can push to any client.

## Evidence & examples

From [[Sources/system-design-masterclass-01]]: all four patterns introduced in the context of the online/offline indicator. The heartbeat (POST `/heartbeat` every 10s) is effectively client-side short polling in reverse — the client initiates periodically rather than the server.

## Tensions & counterarguments

- WebSocket connections are stateful — load balancers must either pin sessions to a server (sticky sessions) or a shared broker is needed. This complicates horizontal scaling.
- SSE has per-domain connection limits in browsers under HTTP/1.1.
- Long polling is a workaround for environments where WebSocket is unavailable — it carries the overhead of repeated connection establishment.

## Related

- [[Topics/system-design]]
- [[Sources/system-design-masterclass-01]]
