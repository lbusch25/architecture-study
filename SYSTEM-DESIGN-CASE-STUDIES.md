# System Design Case Study Bank

Pick one per month (per `STUDY-PLAN.md`), do a design pass (requirements, high-level
architecture, data model, scaling/failure points), then implement a scoped-down version
against real AWS and/or Azure (and Terraform for both where it makes sense) so the design
turns into something deployed, not just a diagram. Deliberately more options than months
available — pick what's a stretch from what you already know, and skew toward ones that let
you reuse/extend the flagship portfolio project rather than always starting fresh.

## E-commerce / Marketplace
- Online marketplace checkout & inventory system (high-relevance to Carvana domain)
- Flash-sale / high-contention inventory reservation system
- Product search & recommendation pipeline for an e-commerce site
- Multi-warehouse order fulfillment & routing system
- Vehicle/asset listing and search platform (VIN-style unique inventory, Carvana-adjacent)
- Auction/bidding platform with real-time price updates

## Payments / Fintech
- Payment processing system with idempotency and reconciliation
- Ledger/double-entry accounting system
- Fraud detection pipeline (streaming + rules/ML scoring)
- Peer-to-peer payments app (Venmo/Cash App style)
- Subscription billing & dunning system
- Loan/credit underwriting workflow engine (Carvana-adjacent)

## Documents / Credentials / EdTech
- Digital credential issuance & verification system (Parchment-adjacent)
- E-signature workflow system (DocuSign-style)
- Large-file document storage, versioning, and access-control system
- Online assessment/exam delivery system with integrity checks (Instructure-adjacent)
- Learning content recommendation & progress tracking system

## Messaging / Real-Time
- Chat application with presence, delivery receipts, multi-device sync
- Real-time notification/fan-out system (push, email, SMS, in-app)
- Collaborative document editing (Google Docs-style OT/CRDT)
- Video conferencing signaling & media routing system
- IoT device command-and-control system with real-time telemetry

## Streaming / Media
- Video-on-demand streaming platform (encoding, CDN, adaptive bitrate)
- Live-streaming platform with low-latency delivery
- Podcast/audio distribution and analytics platform
- Image/video upload pipeline with async processing (thumbnails, transcoding, moderation)

## Data / Analytics
- Real-time analytics dashboard over streaming events
- Data warehouse ingestion pipeline (batch + streaming, CDC from OLTP)
- Log aggregation and observability platform (metrics/logs/traces)
- A/B testing / experimentation platform
- Rate limiter / API quota system as a shared platform service
- Feature store for ML models

## Search / Discovery
- Full-text search engine for a large product/document catalog
- Geo-spatial search system (nearby drivers, stores, inventory)
- Autocomplete/typeahead service at scale

## Identity / Platform Infra
- Multi-tenant SaaS authentication & authorization system (Coupa-adjacent)
- API gateway with auth, rate limiting, and routing for a microservices platform
- Secrets management / workload identity system (ties to your WLI exposure at Carvana)
- Feature flag / config service used across many microservices
- Multi-region active-active platform with data residency constraints
- Internal developer platform (IDP) / self-service infra provisioning system

## Logistics / IoT
- Ride-sharing dispatch system (rider/driver matching, ETA, surge pricing)
- Fleet/vehicle telemetry ingestion and monitoring system (Carvana-adjacent)
- Delivery route optimization and tracking system
- Parking/EV-charging reservation system

## Social / Consumer
- Social media feed generation system (fan-out on write vs read)
- URL shortener / link-tracking service (classic, good for a fast low-stakes rep)
- Event ticketing system with seat-holds and overselling prevention
- Ride/room booking system with double-booking prevention (Airbnb-style)

## AI / Agentic Systems (ties to the "AI Driven Development" thread)
- Retrieval-augmented Q&A system over a private document corpus
- Multi-agent orchestration platform (job queue + agents + tool calls)
- LLM-backed customer support system with escalation to humans
- MCP server exposing an internal platform's APIs to AI agents (can literally reuse the flagship project's MCP layer from `STUDY-PLAN.md`)

## Usage notes
- Rotate categories rather than doing them in list order — breadth across domains is more valuable than depth in one.
- For each pick, explicitly decide: what's the AWS-native version, what's the Azure-native version, and what's the cloud-agnostic (Terraform-portable) version. That three-way framing is the whole point given the cloud-agnostic goal.
- Bias toward case studies that can bolt onto the flagship portfolio project (event-driven core, multi-cloud deploy) instead of always spinning up an unrelated demo — fewer, deeper artifacts beat many disposable ones.
