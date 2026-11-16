---
trigger: always_on
---

# 05 — DevOps, Cloud, Network & DevSecOps

## CI/CD Platforms
- **GitHub Actions:** Use reusable workflows, matrix builds, actions pinned to full commit SHAs, and minimal `permissions`. Configure `concurrency` groups (`cancel-in-progress: true` on PRs), enable **Environment Protection Rules** (required reviewers and wait timers for prod), use **OIDC Cloud Auth** (avoiding long-lived AWS/GCP/Azure secrets), and optimize **cache strategies** (`actions/cache` keyed by lockfile hash).
- **GitLab CI:** Structure `.gitlab-ci.yml` with `needs` (**DAG** execution so jobs run in parallel without waiting for entire prior stages), `parallel:matrix`, mark PR jobs as `interruptible: true`, use `resource_group` to prevent deployment race conditions on shared environments, and configure caches and `artifacts:expire_in`.
- **Jenkins:** Use a Declarative `Jenkinsfile` checked into the repository, shared libraries, and ephemeral build agents. Leverage precise `when` conditions, `post` blocks (`always`, `failure`, `cleanup`), pipeline/stage-level `timeout`s, and inject secrets via **credential binding** (automatically masked in console logs).
- **GitOps (ArgoCD / Flux):** Enable automated sync + self-heal + `prune: true`, configure **sync windows** (blocking automated changes during peak hours or change freezes), manage resource `finalizer`s properly, define custom **health assessments** for CRDs, and perform rollbacks via `git revert`.

## Container & Orchestration
- **Docker:** Use multi-stage builds, minimal base images (`distroless`/`alpine`/`chainguard`) pinned by digest (`@sha256`), non-root `USER`, `.dockerignore`, and `HEALTHCHECK`. Build **multi-arch** images (`linux/amd64`, `linux/arm64` via Buildx), optimize **layer caching** (`--mount=type=cache`), and attach **SBOM / provenance attestations** at build time.
- **Docker Compose:** Use for local development parity with `healthcheck`, `depends_on: condition: service_healthy`, resource limits, and isolated networks.
- **Kubernetes (K8s):** Always define CPU/memory `requests` and `limits`, `liveness`/`readiness`/`startupProbe`s, `PDB`s, default-deny `NetworkPolicy`, and strict `securityContext`. Include **topologySpreadConstraints** (spreading pods across AZs/nodes), **priorityClassName** for critical workloads, **PodSecurityAdmission** (`restricted`), per-namespace **ResourceQuota** & `LimitRange`, and tuned **HPA behavior** (`scaleUp`/`scaleDown` `stabilizationWindowSeconds` to prevent replica flapping).
- **Helm:** Version charts, separate `values-<env>.yaml`, run `helm lint`, **helm unittest**, and **helm diff** before upgrades, manage secrets with **helm secrets (SOPS)**, and test charts in CI with `chart-testing` (`ct`) + `kubeconform`.
- **Kustomize:** Use a base + per-environment overlays, leverage reusable **components** for optional features, and use **replacements** (instead of deprecated `vars`) for cross-resource value injection.

## Infrastructure as Code (IaC)
- **Terraform / OpenTofu:** Use versioned modules, encrypted remote state with state locking, and run `fmt`, `validate`, `tflint`, and `plan` in CI. Use declarative `moved` blocks (refactoring without resource destruction) and `import` blocks in code, write automated tests with **terraform test** (`*.tftest.hcl`), add `check` blocks for post-apply health assertions, and use **ephemeral resources** / `ephemeral` variables (Terraform 1.10+) to keep sensitive secrets out of state files.
- **Pulumi / CDK:** Use strict language typing, **stack references** for cross-stack output sharing, encrypted **secrets** in state, **Policy as Code** enforcement (CrossGuard), and the **Automation API** for programmatic provisioning workflows.
- **Ansible:** Ensure roles and playbooks are strictly *idempotent* and pass `ansible-lint`. Test roles in isolation with **Molecule**, execute via **ansible-navigator** inside consistent container **Execution Environments (EE)**, and encrypt secrets with Ansible Vault.
- **Crossplane:** Manage cloud infrastructure via Kubernetes CRDs using modular **Provider Families**, build platform abstractions with pipeline-mode **Composition Functions**, and govern **Managed Resources** reconciliation with strict RBAC.

## Config Management & Secret
- **HashiCorp Vault / OpenBao:** Use dynamic secrets, short leases, and audit logging. Leverage the **Transit Engine** (encryption-as-a-service without storing data), **PKI Engine** (short-lived internal certificates), **Database Engine** (automated dynamic DB credentials), and **Kubernetes Auth Method** backed by ServiceAccount tokens.
- **SOPS:** Encrypt secret files in Git using `age` or Cloud KMS, define strict path/key rules in `.sops.yaml` (**creation rules**), and perform regular **age key rotation** (`sops rotate` / `updatekeys`).
- **External Secrets Operator (ESO):** Synchronize secrets from Cloud Secret Managers or Vault into K8s using `ClusterSecretStore` / `SecretStore`, configure an appropriate `refreshInterval`, and use `PushSecret` when cluster-generated secrets must be backed up to an external vault.
- **Sealed Secrets:** When using `kubeseal`, enforce `strict` scope (bound to namespace and name), back up the master key securely, and perform periodic **controller key rotation**.

## Observability (Metrics, Logs, Traces)
- **Metrics:** Use Prometheus / VictoriaMetrics + Grafana. Optimize heavy queries with **recording rules**, drop high-cardinality labels via **relabeling** (`metric_relabel_configs` for **cardinality management**), and use **remote write** to **Thanos / Grafana Mimir** for multi-cluster long-term storage.
- **Logging:** Collect structured JSON logs (PII-free, enriched with `trace_id`) via **Promtail** / **Fluent-Bit** / Vector into Loki or OpenSearch. Use **LogQL** for log queries and log-derived metrics, configure **index templates** & ILM/retention policies, and apply **log sampling** / filtering to high-volume debug logs.
- **Distributed Tracing:** Deploy the **OpenTelemetry (OTel) Collector** with pipeline **processors** (`batch`, `memory_limiter`, `attributes`/redaction), combine **head-based** and **tail-based sampling** (retaining 100% of error and high-latency traces), and propagate **context** (`traceparent`) and **baggage** across service boundaries.
- **SLO & Error Budget:** Define SLIs and SLOs, configure **multi-window multi-burn-rate alerts** (preventing false pages while rapidly catching error budget exhaustion), track status on an **SLO dashboard**, and include a **runbook link** in every alert.
- **Continuous Profiling:** Run **Pyroscope** or **Parca** (eBPF) in production to continuously profile CPU usage, memory allocations, and lock contention with minimal overhead.

## DevSecOps Pipeline Gates
- **SAST:** Run **Semgrep** (with project-specific **custom rules**), **CodeQL** (`security-extended` **query packs**), and **SonarQube Quality Gates** as required PR merge checks.
- **SCA:** Automate dependency updates via **Renovate** (`renovate.json` with package grouping and safe patch automerge) or **Dependabot security updates**, and block critical CVEs with **OSV-Scanner** / Snyk.
- **Secret Scanning:** Enforce **gitleaks** (with `.gitleaks.toml` **custom rules** for internal token formats) and **trufflehog** (`--only-verified` to prioritize live credentials) in pre-commit hooks and CI.
- **Container Scanning:** Scan container images with **Trivy** (`--severity HIGH,CRITICAL --ignore-unfixed --exit-code 1`) or **Grype** (`--fail-on high`) before pushing to a registry.
- **IaC Scanning:** Scan Terraform, K8s, and Dockerfiles with **Checkov** (including YAML/Python **custom policies**), **KICS queries**, or **tfsec** (use `--soft-fail` only during initial migration phases).
- **DAST:** Run **OWASP ZAP** (fast *baseline scans* on every PR, scheduled *full scans* on staging), **Nuclei** with up-to-date CVE/misconfiguration templates, and **authenticated scans** to test endpoints behind login flows.
- **SBOM:** Generate comprehensive SBOMs using **Syft** (`--scope all-layers`) in **CycloneDX** (accompanied by **VEX** — *Vulnerability Exploitability eXchange* documents) or **SPDX 3.0** format.
- **Signing & Verification:** Sign container images and build artifacts using **Cosign keyless (OIDC Sigstore)** + SLSA provenance **attestations**, and enforce signature verification at cluster admission with **Kyverno `verifyImages`** or Policy Controller.
- **Runtime Security:** Detect runtime container exploits and anomalies using **Falco** (with custom syscall rules) and **Tetragon** (eBPF **TracingPolicy** capable of blocking malicious processes directly in kernel space).

## Deployment Strategy
- **Blue-Green Deployment:** Ensure **database compatibility** between Blue and Green versions, perform **session draining** so active transactions complete cleanly, and switch traffic via **Load Balancer / Ingress weight switching** (instant) rather than DNS switching (subject to TTL caching delays).
- **Canary Release:** Use Argo Rollouts or Flagger with **automated metric analysis** (Prometheus success rate and p99 latency) that triggers **automatic rollback** on degradation, and leverage **traffic mirroring / shadowing** to test production payloads without impacting live users.
- **Rolling Update:** Tune `maxSurge` and `maxUnavailable` for cluster capacity, add a `lifecycle.preStop` hook (e.g., `sleep 5–10s` while endpoints drain from kube-proxy/ingress), and ensure the application handles `SIGTERM` for **graceful shutdown**.
- **Feature Flags:** Use Unleash, LaunchDarkly, or Flagsmith with **targeting rules** (user segments/regions), staged **percentage rollouts**, measured **A/B testing**, and an instant **kill switch** without redeploying.
- **Database Migration:** Enforce backward-compatible **expand-contract** migrations, and use **shadow writes** (dual-writing) with data consistency verification when migrating critical tables.

## Reliability, Scaling & DR
- **Load Balancing & Traffic Management:** Enable **connection draining** (deregistration delay), **slow start** (gradual warm-up for new instances), **circuit breaking** (outlier detection in Envoy/ALB), and cap **retry budgets** to prevent *retry storms*.
- **Auto-Scaling:** Combine **HPA** (with tuned scaling `behavior` and custom metrics), **KEDA** (`ScaledObject` driven by Kafka/RabbitMQ/SQS queue depth or cron schedules), and **Karpenter** (`NodePool` & `EC2NodeClass` for rapid bin-packing and node provisioning in seconds).
- **Resilience Patterns:** Implement **retry + exponential backoff + jitter**, propagate **timeout budgets** across microservice calls, isolate resources with **bulkheads**, and apply **load shedding** (rejecting low-priority requests with `503` under extreme overload).
- **Chaos Engineering:** Run scheduled fault-injection experiments using **Chaos Mesh workflows** or **Litmus ChaosEngine** (pod kill, network latency/loss, disk fill, DNS faults) and conduct team **Game Days**.
- **Backup:** Enable **PITR (Point-in-Time Recovery)** with continuous **WAL archiving**, automated **snapshot lifecycle** policies, **cross-region replication** (with immutable object lock against ransomware), and automated **restore drills** to verify backup integrity.
- **Disaster Recovery (DR):** Define a per-tier **RTO/RPO matrix**, maintain tested **failover runbooks**, and architect **multi-region** deployments (*Active-Passive* warm standby or *Active-Active* with global routing and managed data consistency).
- **Capacity Planning:** Stress-test system limits using **k6 scenarios** (ramping arrival rates), **Locust LoadTestShapes**, **Vegeta attacks** for constant-throughput benchmarks, and long-duration **soak tests** to uncover memory or connection leaks.

## Platform Engineering & Multi-Cloud
- **Internal Developer Platform (IDP):** Build a **Backstage** portal with **Software Templates** (*Golden Paths* that scaffold new services complete with CI/CD & K8s manifests), standardized `catalog-info.yaml` metadata, integrated documentation (**TechDocs**), and **Service Scorecards** to track production and security maturity.
- **FinOps & Cost Governance:** Enforce mandatory **cost allocation tags** (validated in IaC), per-team **showback / chargeback** reporting, automated **rightsizing** (VPA/Kubecost/OpenCost), and **Savings Plans / Reserved Instances** + Spot nodes for fault-tolerant workloads.
- **Cloud Hardening & Governance:** Establish a standardized multi-account **Landing Zone** (**AWS Control Tower** / GCP Cloud Foundation / Azure Landing Zones) and enforce preventive organization-wide guardrails using **AWS SCPs (Service Control Policies)**, **GCP Organization Policies**, and **Azure Policy**.

## Networking & Infrastructure Security
- **VPC & Connectivity:** Design hub-and-spoke topologies with **AWS Transit Gateway** or controlled **VPC Peering**, use **PrivateLink / VPC Endpoints** to reach cloud services without traversing the public internet, deploy **HA NAT Gateways** across AZs, and enable **VPC Flow Logs** for traffic and anomaly analysis.
- **Firewall, WAF & DDoS:** Use **Security Group referencing** (allowing traffic from source SG IDs rather than static IPs), **stateless NACLs** as subnet boundary guardrails, **WAF Rule Groups** (managed OWASP rules + rate-based rules), and advanced DDoS protection (**AWS Shield Advanced** / Cloudflare Magic Transit).
- **DNS & CDN:** Configure health-checked **Route53 failover & latency-based routing**, secure origin buckets behind CDNs using **CloudFront Origin Access Control (OAC)**, and cleanly separate **Cache Policies** from Origin Request Policies.
- **TLS & Cryptographic Identity:** Automate certificate lifecycles with **cert-manager** (`Issuer` / `ClusterIssuer`) using **ACME DNS-01** validation (for wildcard/internal ingresses), and issue cryptographic workload identities via **mTLS SPIFFE / SPIRE**.
- **Zero Trust Network:** Implement a **BeyondCorp** access model using identity-aware **Tailscale ACLs** or **Cloudflare Access policies** (OIDC + device posture checks) in place of legacy flat-network VPNs.
- **Service Mesh:** Use **Istio** (`VirtualService`, `DestinationRule`, `PeerAuthentication` with STRICT mTLS), **Linkerd** (lightweight `AuthorizationPolicy`), or **Cilium** (identity-aware L3–L7 **CiliumNetworkPolicy** powered by eBPF without sidecar overhead).

## Database DevOps (DBRE)
- **High Availability (HA):** Configure production databases with **Multi-AZ synchronous standby**, **read replicas** to offload read traffic, explicit **failover priority** tiers, and application-side **connection retry** with backoff during failovers.
- **Schema Migration Automation:** Manage schema lifecycles with **Flyway** (`baseline`, checksum validation), **Liquibase** (XML/YAML/SQL `changelog`s), or **Alembic** (manually reviewed `autogenerate` revisions), and run **Atlas `migrate lint`** in CI to catch dangerous DDL operations (table locks or data loss) before merge.
- **Connection Management:** Deploy **PgBouncer** (preferring **`transaction` pool mode** for high scalability while accounting for prepared statement constraints vs `session` mode) or **ProxySQL** (with **query rules** for read/write splitting and query caching), and enable **connection leak detection** in application pools.
- **Database Monitoring & Maintenance:** Monitor the **slow query log**, **`pg_stat_statements`** (top queries by total/mean execution time), **replication lag**, **table/index bloat** + **autovacuum** tuning, and alerts on **lock contention** / blocking queries.

## Incident Management & Governance
- **On-Call & Escalation:** Manage rotation schedules in **PagerDuty / Opsgenie / Grafana OnCall** with multi-tier **escalation policies** and a clear **severity matrix** (`SEV-1` through `SEV-4`).
- **Runbooks & Automation:** Link **step-by-step runbooks** directly in alert payloads, complete with fast diagnostic commands and **automated remediation** (auto-heal) scripts for well-understood recurring issues.
- **Blameless Post-Mortem:** Use a standardized **blameless template** (impact summary, detection-to-recovery timeline / MTTD / MTTR, **5 Whys** root cause analysis), track **action items** with owners and due dates in the issue tracker, and hold periodic **follow-up reviews**.
- **Compliance & Audit Automation:** Automate **SOC 2 evidence collection**, **ISO 27001 control** mapping, strict **PCI-DSS Cardholder Data Environment (CDE) scope** isolation, and **GDPR / Data Privacy DPIA (Data Protection Impact Assessment)** documentation through Policy as Code and immutable audit logs.
