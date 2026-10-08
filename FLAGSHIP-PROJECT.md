# Flagship Portfolio Project

The one project this whole plan is built around demonstrating — built incrementally over the
full plan duration, not rushed. Bias toward extending this rather than starting something new
whenever a monthly system design case study comes up (see `SYSTEM-DESIGN-CASE-STUDIES.md`).

## The concept

A cloud-agnostic **core platform** — API Gateway + Auth/IAM + an event-driven core + an
observability pipeline + an MCP/AI-agent layer — deployed to both AWS and Azure via Terraform,
containerized for EKS/AKS, eventually multi-region. Real, functional applications (a chat app,
media uploads) get layered on top of the platform once it exists, rather than building disposable
demo apps independently.

## Why this, not another workflow/orchestration engine

The first instinct was to rebuild a more general version of the Instructure workflow engine —
rejected on reflection, because that would be *redundant depth* on an already deeply-proven
strength (see `RESUME-PROJECT-DEEP-DIVES.md`), not new range. This project fills the actual gaps:
the multi-cloud infrastructure pattern itself (not proven anywhere yet), and a genuinely different
architectural shape (protocol/policy-evaluation, not task-graph). It also positively reconnects
two things that otherwise have no outlet: the Coupa security background (disliked as a job,
genuinely useful as knowledge) and the startup's SOC2 compliance work (access control is a core
SOC2 control domain — V1 below is directly dual-purpose with that real, paid work).

## Core architecture

```
                 ┌─────────────────┐
  Requests  ───▶ │   API Gateway    │  (routing, rate limiting)
                 └────────┬─────────┘
                          │ enforces
                 ┌────────▼─────────┐
                 │   Auth / IAM     │  (OIDC token validation, RBAC/ABAC policy eval)
                 └────────┬─────────┘
                          │ every decision emits an event
                 ┌────────▼─────────┐
                 │  Event-driven    │  SQS/SNS/EventBridge  ↔  Service Bus/Event Grid
                 │      core        │
                 └────────┬─────────┘
                ┌─────────┴──────────┐
     ┌──────────▼─────────┐  ┌───────▼────────────┐
     │ Audit/Observability │  │   MCP / AI layer   │
     │ pipeline (SOC2-     │  │ ("list auth        │
     │ relevant logs,      │  │  failures for X",  │
     │ metrics/dashboards) │  │  "check rate limit  │
     │                     │  │   for this key")    │
     └─────────────────────┘  └────────────────────┘
```

## Roadmap

Each version builds on the last. Pacing note below — this spans most of the plan, not a near-term
sprint.

- **V1 — Auth/IAM + event core.** Basic OIDC token issuance/validation, RBAC policy evaluation,
  every auth decision emitting an event onto the bus. Complete, demonstrable on its own.
  **Dual-purpose**: this access-control design work directly informs the real SOC2 controls
  needed at the startup — not purely portfolio hours.
- **V2 — API Gateway.** Routing and rate limiting, enforcing V1's auth on every request.
  Open decision: build a thin custom gateway (more architectural judgment on display) vs. wrap
  the native cloud services (AWS API Gateway / Azure API Management) via Terraform (faster, still
  real multi-cloud IaC skill, less novelty).
- **V3 — Observability/audit pipeline**, consuming the event stream already flowing by this
  point. Dashboards, alerting, the SOC2-relevant audit trail.
- **V4 — MCP layer**, once there's a real system underneath worth exposing to an agent.
- **V5 — Multi-region deployment.** Active-active vs. active-passive; design the real
  consistency/replication tradeoff deliberately (does an auth decision in one region need to be
  visible to a gateway decision in another?). Fills in the still-empty Rosetta Stone entries:
  DynamoDB Global Tables vs. Cosmos DB multi-region writes (replicating the auth/policy store),
  and Route 53 latency routing vs. Azure Traffic Manager/Front Door (failover/routing).
  Directly exercises Availability/Fault Tolerance/DR from `STUDY-TOPICS.md`.
- **V6 — Real-time chat application**, built on top of V1-V2 (uses Auth/IAM, goes through the
  Gateway). Adds a new transport concern — WebSocket connection management for live messaging —
  extending the real WS experience already gained from the Carvana test-drive cube.
- **V7 — RAG system for querying chat logs**, built directly on top of V6's data. Chunk/embed
  chat messages, store in a vector store — **pgvector on Postgres specifically**, not a
  cloud-specific vector service, since it works identically on AWS RDS and Azure Database for
  PostgreSQL and keeps the cloud-agnostic story intact. The retrieval step is **authorization-scoped**
  through the V1 Auth/IAM layer — a user can only retrieve/search chunks from channels they're
  actually authorized to see, which is a more realistic design than naive "search everything," and
  a direct tie back to the core platform instead of a bolt-on. Expose as a tool through the V4 MCP
  layer, so an agent can query chat history on a user's behalf. Matches the case-study bank entry
  "Retrieval-augmented Q&A system over a private document corpus," and is a high-signal, currently
  in-demand pairing given the AI-driven development thread already in `STUDY-PLAN.md`.
- **V8 — Video/document upload + processing pipeline.** Object storage (fills in the S3 ↔ Blob
  Storage Rosetta Stone entry), async processing (transcoding, document scanning). Maps onto
  existing case-study bank entries ("Image/video upload pipeline with async processing,"
  "Video-on-demand streaming platform").
- **V9 — Analytics data-streaming layer**, separate from the V1 auth-audit event bus, consuming
  chat/media activity (message volume, engagement metrics, content-moderation flagging). This is
  the natural home for a true streaming platform (Kinesis / Event Hubs, or self-hosted
  Kafka/Redpanda), filling in that Rosetta Stone entry with real depth instead of just a name
  mapping.

## Pacing note

V1-V5 roughly track the existing `STUDY-PLAN.md` Phase 2-5 cadence. **V6-V9 are "whenever the
monthly case-study cadence gets there," explicitly not near-term pressure.** The scope here is
meant to span most of the plan — treating it as something to rush would recreate the exact
capacity problem already flagged elsewhere in the plan.

## Open decisions (not yet settled)

- [ ] Primary language for the core platform — default assumption is C# to reinforce the current
      day-job skill (consistent with the "keep C# growing via the day job" note in Phase 4 of
      `STUDY-PLAN.md`), with the NestJS/TypeScript practice piece living in a smaller component
      rather than the whole platform. Confirm or adjust.
- [ ] Custom-built API Gateway vs. wrapping native cloud services (see V2 above).
- [ ] Which exact SOC2 control area is most urgent at the startup right now, to make sure V1's
      design work lines up with real near-term needs there, not just in general.

## Ties to other docs

- **Case studies incorporated** (from `SYSTEM-DESIGN-CASE-STUDIES.md`): API gateway with
  auth/rate limiting/routing; chat application with presence/delivery receipts; retrieval-augmented
  Q&A over a private document corpus; image/video upload pipeline with async processing;
  multi-region active-active platform.
- **Rosetta Stone entries this will fill in with real depth**: DynamoDB Global Tables ↔ Cosmos DB
  multi-region, Route 53 ↔ Traffic Manager/Front Door, S3 ↔ Blob Storage, Kinesis ↔ Event Hubs.
- **`STUDY-TOPICS.md` topics covered**: Event-Driven Architecture, Authentication, Databases,
  Networking, Availability, Fault Tolerance, Disaster Recovery, Performance, Containerization,
  AI-driven development (the MCP layer).
- **Resume material**: once built out, this becomes the primary candidate for a new
  `RESUME-PROJECT-DEEP-DIVES.md` entry — a personal project, not a work one, so framed as "built to
  demonstrate X" rather than a business-driver story like the Carvana/Instructure entries.
