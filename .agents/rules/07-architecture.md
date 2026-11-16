---
trigger: always_on
---

# 07 — Architecture & Advanced

## State Management & System Architecture
- Choose one architectural pattern suited to the domain complexity (MVC / MVVM / MVI / Clean / Hexagonal / Modular Monolith) and apply it consistently.
- Strictly separate **Domain** (pure business rules), **Data/Infrastructure**, and **Presentation/UI** layers. Dependencies must always point inward toward the Domain layer.
- Prefer local state first and elevate to global state only when needed across features. Avoid deep prop drilling, maintain a *single source of truth*, and eliminate duplicate state.
- Isolate all side effects (network, DB, storage, analytics) inside service/repository layers rather than inside UI components.

## Event-Driven Architecture
- **Event Sourcing & CQRS:** Use *Event Sourcing* when a complete, immutable audit history of state transitions is required, and pair it with **CQRS** (*Command Query Responsibility Segregation*) to separate write models (validation/mutation) from read-optimized view projections.
- **Event Schema Versioning & Evolution:** Define event contracts in a Schema Registry (Protobuf / Avro / JSON Schema). Ensure schema changes are *backward- and forward-compatible* (add optional fields, never rename/remove fields or change types in place, and use *upcasting* for legacy event versions).
- **Delivery Semantics:** Understand the trade-offs between *At-most-once* (fast, messages may be lost), *At-least-once* (production standard: delivery is guaranteed but duplicates can occur → **consumers must be idempotent**), and *Exactly-once* (expensive, typically achieved via *at-least-once* delivery combined with idempotent deduplication and transactional state updates at the consumer).
- **Saga Pattern (Distributed Transactions):** Coordinate cross-service transactions using **Sagas** (*Choreography* via domain events for simple flows, or centralized *Orchestration* for complex workflows) complete with **compensating transactions** (semantic undo actions) when a step fails.
- **Transactional Outbox Pattern:** Never write to the database and publish directly to a message broker without atomicity; persist outgoing events in an `outbox` table within the same DB transaction, then publish them reliably via a relay worker or CDC stream (Debezium).

## Distributed System Patterns
- **Circuit Breaker:** Protect calls to external dependencies with a state machine (`Closed` → `Open` when failures exceed a threshold → `Half-Open` to probe recovery) and provide a safe **fallback** (cached data, default value, or graceful degradation).
- **Bulkhead Pattern:** Isolate resources (thread pools, connection pools, semaphores, queues) per dependency or feature so a slow or failing upstream service cannot exhaust the entire application's capacity.
- **Retry Pattern:** Use **exponential backoff + random jitter** with a strict **max attempts** cap, and only retry **idempotent** operations or transient network/5xx errors (preventing *retry storms*).
- **Timeout & Deadline Propagation:** Enforce an explicit **timeout budget** on every incoming request and propagate the remaining deadline (*deadline propagation* via `context.Context`, gRPC deadlines, or HTTP headers) to all downstream calls so services stop working on canceled requests.
- **Leader Election:** Use a distributed consensus store (**etcd** / **ZooKeeper** / Consul / K8s Lease) with TTL leases to elect a single active coordinator, and guard against **split-brain** scenarios using *quorums* and *fencing*.
- **Distributed Lock:** When mutual exclusion across nodes is required (e.g., Redis / Redlock or etcd), always bound the lock TTL and issue a monotonically increasing **fencing token** validated by the target storage/resource so a paused process (GC pause or network delay) cannot corrupt state after its lock expires.
- **Two-Phase Commit (2PC) vs Saga:** Avoid **2PC** across microservices because it holds locks, introduces coordinator single points of failure, and degrades throughput; prefer **Saga + Idempotency** for distributed workflows.

## Data Consistency
- **Strong vs Eventual Consistency:** Require *Strong Consistency* (ACID / linearizability) for financial balances, critical inventory, and authorization state; use *Eventual Consistency* for feeds, analytics, search indexes, and notifications to maximize availability and reduce latency.
- **CAP Theorem & Real-World Implications (PACELC):** Because network partitions (`P`) are inevitable in distributed systems, explicitly decide per feature whether to favor **CP** (reject requests to preserve consistency) or **AP** (continue serving requests with potentially stale data), as well as the *Latency vs Consistency* trade-off during normal operation.
- **Read-Your-Writes & Causal Consistency:** Ensure users immediately see their own updates by routing their subsequent reads to the primary DB briefly, using *session stickiness*, or tracking a *version/LSN token* (*causal consistency*).
- **Conflict Resolution:** In multi-node replication or offline-first sync, resolve write conflicts deterministically using **Last-Write-Wins (LWW)** (accounting for clock skew), **Vector Clocks / Version Vectors** to detect concurrent edits, or **CRDTs** (*Conflict-free Replicated Data Types*) for automatic mathematical convergence.
- **Idempotency Architecture:** Design deterministic `Idempotency-Key` values (UUIDs or business-key hashes per operation), persist execution state (`IN_PROGRESS`, `COMPLETED` with cached response payload) in an atomic store, and enforce a cleanup **TTL** (e.g., 24–72 hours).

## Multi-Tenancy
- **Data Isolation Models:** Select based on compliance, scale, and cost requirements:
  - *Shared DB + Shared Schema (Row-Level):* Most cost-effective; require a `tenant_id` column and index on every table and enforce isolation via **PostgreSQL Row-Level Security (RLS)** or mandatory ORM query filters.
  - *Shared DB + Separate Schema:* Moderate logical isolation per tenant within a shared database cluster.
  - *Database / Infrastructure per Tenant:* Highest isolation (security, backup, noisy-neighbor immunity) for enterprise tenants.
- **Tenant Context Propagation:** Extract the verified tenant identity from JWT claims or API Gateway headers/subdomains, and propagate it explicitly through request `context` (never store tenant state in mutable globals that can leak across requests).
- **Noisy Neighbor Mitigation:** Enforce per-`tenant_id` **rate limits**, **concurrency limits**, *fair queuing*, and **resource quotas** so a burst from one tenant cannot degrade service for others.
- **Data Residency & Sovereignty:** Support geography-aware routing and storage (*region-specific shards/buckets*) to comply with data sovereignty regulations (such as GDPR or local data protection laws).

## Auth, Identity & Security Architecture
- **Auth & Sessions:** Hash passwords with `Argon2id`/`bcrypt`. Use short-lived access tokens + refresh token rotation + revocation lists. Never store secrets in JWT payloads, use standard OAuth2/OIDC libraries, require MFA for sensitive actions, rate-limit login endpoints, and enforce authorization on the server.
- **Zero Trust Architecture:** Never trust traffic solely based on network location (*BeyondCorp model*); authenticate and authorize every service-to-service request using **mTLS** and cryptographic workload identities (**SPIFFE / SPIRE**).
- **Secrets Management:** Use dynamically generated secrets with *short leases*, *automated rotation*, and comprehensive *audit logs* via Vault or Cloud KMS.
- **Supply Chain Architecture:** Verify **SBOMs**, **SLSA provenance**, and **Sigstore/Cosign** cryptographic signatures before allowing artifacts to deploy to production.
- **Threat Model Integration:** Perform **per-component STRIDE analysis**, build **attack trees** for critical asset paths, and document **trust boundaries** in every Architecture Decision Record (ADR).

## File & Media Handling
- Validate file types (magic bytes + MIME type + extension) and size limits on the server, and rename uploaded files to random UUIDs.
- Scan public uploads for malware, store files in Object Storage rather than local application disks, resize/compress images asynchronously, issue short-lived *pre-signed URLs* for private file access, and prevent path traversal.

## Payment & Financial Systems
- Never store raw payment card data; rely on PCI-DSS compliant Payment Gateway tokenization.
- Enforce **Idempotency Keys** on all financial transactions, verify cryptographic signatures + timestamps on payment webhooks, record transactions in an *immutable double-entry ledger*, run automated reconciliation jobs, and represent monetary values using **Integer** (minor units/cents) or exact **Decimal** types (never `float`) alongside an explicit `ISO 4217` currency code.

## Realtime & Messaging
- **WebSocket / SSE:** Authenticate during the handshake, implement heartbeat ping-pong frames, and reconnect automatically using exponential backoff + jitter.
- **Message Queues (RabbitMQ / Kafka / SQS):** Build **idempotent** consumers, configure Dead Letter Queues (DLQ), understand partition-level ordering and delivery guarantees, and partition/shard topics for horizontal scale.

## Caching Strategy
- Define a clear cache invalidation strategy (TTL, write-through, or event-driven invalidation) with consistent, versioned cache keys.
- Never cache user-specific private data in public/CDN caches. Configure connection pools, timeouts, stampede protection (`singleflight`), and graceful fallbacks when Redis/Memcached is unavailable, and set accurate HTTP cache headers (`ETag`, `Cache-Control`).

## Error Monitoring, Alerting & Advanced Observability
- **Error Tracking & Health:** Integrate Sentry/Rollbar with issue grouping and release tags, expose liveness/readiness probes, and maintain actionable runbooks and blameless post-mortems.
- **Distributed Tracing:** Standardize context propagation on **W3C Trace Context** (`traceparent`, `tracestate`) and **Baggage**, combining *head-based sampling* with **tail-based sampling** in the OTel Collector so 100% of error and high-latency traces are captured.
- **Metrics Cardinality Management:** Design metric labels carefully (never put unbounded values like `user_id`, `email`, or `request_id` into Prometheus labels), use pre-aggregation/recording rules, and enforce retention and downsampling policies.
- **SLO, SLI & Error Budget:** Define clear SLIs (availability, p95/p99 latency) and SLO targets, alert on **multi-window burn rates** (paging only when the error budget is actively burning), and manage operational **toil budgets**.
- **Continuous Profiling:** Run low-overhead eBPF continuous profilers (**Pyroscope** / **Parca**) in production to inspect CPU flamegraphs, memory allocations, and goroutine/thread contention in real time.

## Feature Flags, Release & Deployment Architecture
- **Cell-Based Architecture:** For massive-scale systems, partition infrastructure into self-contained **cells** (each serving a subset of tenants/users end-to-end) to contain failure **blast radius** and simplify *cell routing*.
- **Blue-Green vs Canary vs Shadow:**
  - *Blue-Green:* Instant traffic cutover and fast rollback; requires 2x capacity during cutover and backward-compatible DB schemas.
  - *Canary:* Gradual percentage rollout validated against live production telemetry.
  - *Shadow / Dark Traffic:* Asynchronously duplicates production requests to a new version to validate performance and correctness without affecting live user responses.
- **Database Migration Patterns:** Enforce backward-compatible **expand-contract** migrations paired with **dual-write / shadow write** and data consistency checks when migrating critical tables with zero downtime.
- **Feature Flag Architecture:** Manage the complete *flag lifecycle* (creation → staged targeting/rollout → 100% GA → cleanup of stale flag code), support contextual targeting, and provide an instant **kill switch**.

## Compliance & Privacy
- Practice *data minimization*, manage user consent and privacy policies, provide self-service data export and deletion flows, encrypt data *at rest* and *in transit*, automate data retention purging, and record immutable audit logs for administrative and sensitive actions in compliance with GDPR / data privacy laws.

## Advanced Mobile Architecture
- Configure verified Deep Links & Universal Links, manage application lifecycle transitions to conserve battery, request push notification permissions in context with secure payloads, monitor binary size, integrate crash reporting, and design offline sync with deterministic conflict resolution.

## Design System & UI Engineering
- Use consistent design tokens (color, spacing, typography) and reusable components supporting both Dark and Light modes.
- Always implement loading, empty, error, and success states, provide immediate visual feedback on user actions, and ensure forms have explicit labels, inline validation errors, and preserve user input on submission failure.

## Legal & Open Source Licensing
- Audit dependency licenses (prefer MIT, Apache-2.0, BSD; never mix strong copyleft GPL/AGPL packages into closed-source commercial code), include license attribution notices where required, run automated license scans in CI, and publish Terms of Service + Privacy Policy for public applications.

## Networking, Protocols & Advanced API Design
- **Resilience & HTTP Semantics:** Use HTTP methods, status codes, and headers semantically. Always configure explicit timeouts, retries (exponential backoff + jitter), and circuit breakers on external calls.
- **Advanced REST:** Follow the **Richardson Maturity Model** (minimum Level 2 with resource URIs, proper HTTP verbs, and accurate status codes, plus Level 3 **HATEOAS** where dynamic state transitions benefit clients), standard `RFC 7807 Problem Details` error payloads, and `Idempotency-Key` support on `POST`/`PATCH`.
- **Advanced GraphQL:** Eliminate **N+1 query problems with DataLoader**, enforce **query depth & complexity limits**, use **Persisted Queries** (allowlisted query hashes) for security and CDN caching, and adopt **Federation** when unifying multiple microservice subgraphs.
- **Advanced gRPC:** Leverage unary and **streaming RPCs** (client, server, bidirectional), propagate **deadlines/timeouts** end-to-end, enable **Server Reflection** in non-production environments for debugging, and implement the standard **gRPC Health Checking Protocol**.
- **Webhook Architecture:** When sending or receiving webhooks, include an **HMAC/asymmetric signature + timestamp** (preventing replay attacks), process deliveries asynchronously through an **idempotent handler** (deduplicating by `event_id`), apply an exponential-backoff **retry policy**, and provide a **DLQ** with manual replay support.

## Concurrency & Parallelism
- Prevent race conditions, deadlocks, and starvation by keeping locks minimal and acquiring them in a consistent order.
- Never block the event loop or main thread; offload CPU-heavy work to worker pools, apply backpressure to queues, and verify concurrent code with stress tests and race detectors (`-race` / TSan).

## Memory & Resource Management
- Always release resources (files, DB connections, network streams) using `defer`/`with`/`using`/`finally`, clean up listeners/timers/subscriptions on teardown, pool expensive objects/connections, bound buffer and queue sizes, and profile memory usage regularly.

## Build & Dependency Management
- Always commit lockfiles with pinned dependency versions, minimize external dependencies, enforce reproducible cached builds, and run automated CVE audits in CI.

## Monorepo & Modularity
- Define strict module boundaries with unidirectional dependencies (no circular dependencies), version shared packages, enable incremental builds and tests, assign `CODEOWNERS` per module, and standardize tooling across the workspace.

## Environment & Configuration
- Inject all configuration via environment variables and validate at startup. Separate `dev`, `staging`, and `prod` configs, never commit real `.env` files, use a Secret Manager with scheduled rotation in production, and support dynamic configuration reloads where needed.

## Data Migration & Backward Compatibility
- Follow the **expand-contract** pattern (add new schema elements first, backfill/migrate data, then remove legacy elements in a subsequent release). Test migrations against production-like datasets, never rename or drop columns in place without a transition phase, and always verify backups before major migrations.

## Search & Indexing
- Use a dedicated search engine (`Elasticsearch`/`OpenSearch`/`Meilisearch`/`Typesense`) for full-text search and typo-tolerant fuzzy matching; keep indexes synchronized from the primary DB via events/CDC queues, enforce pagination, and avoid `LIKE '%...%'` scans on large tables.

## Background Jobs & Schedulers
- Make all background jobs **idempotent** with retries, Dead Letter Queues, and failure alerting. Trigger scheduled tasks via cron/queues (never `sleep` inside HTTP request handlers), configure job priorities and concurrency limits, break massive batch jobs into smaller chunks, and include a correlation/job ID in every log line.

## API Clients & SDKs
- When building SDKs or API clients, provide configurable timeouts, retry with backoff, clean error mapping, strict SemVer versioning (no breaking changes in minor/patch releases), automatic rate-limit handling (`Retry-After`), a descriptive `User-Agent`, and runnable code examples.

## Debugging & Troubleshooting
- Propagate `correlation_id` across services in structured logs, master debuggers/tracers/profilers, always reproduce bugs first (*reproduce first*) with a failing test before writing a fix, and maintain a team troubleshooting guide/FAQ.

## Refactoring & Technical Debt
- Refactor in small, incremental steps backed by unit tests, track and prioritize technical debt, never mix large architectural refactors into critical feature/hotfix PRs, and practice the *Boy Scout Rule* (remove dead code and stale comments).

## Scaling & Reliability
- Design stateless services for effortless horizontal scaling (storing session/shared state in centralized stores), deploy behind health-checked load balancers, implement *graceful degradation* when non-critical dependencies fail, test backups and disaster recovery against RTO/RPO targets, and perform regular capacity planning.

## Cost Architecture / FinOps
- **Cost Allocation:** Enforce a consistent *resource tagging* strategy (`env`, `service`, `team`, `tenant`) to enable accurate per-team/product cloud **showback** and **chargeback** reporting.
- **Right-Sizing & Tiering:** Select optimal instance families/architectures (ARM/Graviton vs x86) for each workload, automate **storage tiering** (hot → warm → cold/archive), and blend **Reserved Instances / Savings Plans** (for steady baseline load) with **On-Demand / Spot Instances** (for bursty or stateless batch workloads).
- **Cost Anomaly Detection:** Configure multi-threshold **budget alerts** and automated **cost anomaly detection & forecasting** so runaway loops or misconfigured autoscalers are caught before bills spike.
- **Unit Economics:** Measure architectural efficiency through business-aligned metrics such as **cost per request**, **cost per transaction**, or **cost per active tenant** as the system scales.

## AI / LLM Integration
- Decouple and version prompts outside application code, enforce token quotas and request timeouts, validate LLM outputs against strict schemas (JSON Schema / Zod / Pydantic) before use, redact PII/secrets before calling external models, and configure rate limits, retry backoff, model fallbacks, and token cost tracking.

## Accessibility (a11y) & Internationalization (i18n)
- Meet WCAG AA color contrast ratios, visible keyboard focus indicators, meaningful image `alt` text, and explicit form labels. Externalize all user-facing strings into i18n resource files, support RTL layouts where needed, and format dates, numbers, and currencies according to the user's locale.

## Advanced Performance Engineering
- **Core Principles:** Profile before optimizing, cache expensive operations, lazy-load non-critical assets, keep bundle sizes small, and enforce pagination everywhere.
- **Structured Load Testing:** Automate performance testing with **k6 scenarios** covering *smoke tests* (functional verification), *load tests* (expected normal/peak traffic), *stress tests* (finding breaking points), *spike tests* (sudden traffic surges), and *soak tests* (long-duration runs to detect memory leaks) gated by explicit **pass/fail thresholds** (`p95 < X ms`, `error_rate < 0.1%`).
- **Deep Profiling:** Diagnose bottlenecks using **CPU profilers** (`pprof`, `async-profiler`, Flamegraphs), **Memory profilers** (heap dumps, allocation rates, GC pause/promotion analysis), and **lock contention / mutex profilers**.
- **Database Query Optimization:** Inspect execution plans with `EXPLAIN (ANALYZE, BUFFERS)`, apply **index tuning** (composite, covering, and partial indexes), leverage **partition pruning** on large tables, and use **materialized views** for expensive read-heavy aggregations.
- **CDN & Edge Strategy:** Maximize **cache hit ratios** through normalized cache keys, fast **cache invalidation / purge-by-tag** workflows, **Origin Shield** layers to protect origins from *cache miss storms*, and **Edge Compute** (Workers/Functions) for low-latency routing, lightweight auth, and localization close to users.
