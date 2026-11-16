---
trigger: always_on
---

# 08 — Checklist per Role

## Programmer
- Consistent naming (`kebab-case` files, `PascalCase` classes/components, `camelCase`/`snake_case` functions/variables, `UPPER_SNAKE_CASE` constants).
- Complete error handling (never swallow errors, attach clear context, separate user-facing error messages from internal logs).
- No duplicated logic (DRY, modular, one responsibility per file/function).
- Relevant tests included (unit tests for business logic, regression tests for bug fixes, named `should_do_x_when_y`).
- Public functions documented (docstrings covering parameter contracts, return values, and error conditions).

## Web
- Responsive design (mobile-first, clear breakpoints, accessible touch targets).
- Semantic HTML + ARIA attributes + meaningful `alt` text + full keyboard navigation.
- Client-side validation (fast UX) + server-side validation (mandatory security boundary).
- Clean state management (local state first, URL state, `localStorage`/`IndexedDB` as appropriate, mandatory loading/empty/error states).
- Strong performance (Core Web Vitals LCP/INP/CLS, lazy loading, code splitting, image & font optimization, caching).
- Full protection against XSS, CSRF, Clickjacking, and CORS misconfigurations (strict CSP, HSTS, `HttpOnly` + `Secure` + `SameSite` cookies).

## Android
- Lifecycle-aware architecture (ViewModel, `repeatOnLifecycle` / `collectAsStateWithLifecycle`, zero memory leaks).
- Respectful permissions (requested in context when the feature is used + clear rationale).
- Offline-first design (Room + DataStore, reliable background sync with WorkManager + conflict resolution).
- R8/ProGuard enabled in production release builds; tokens stored in Android Keystore / Encrypted storage.
- Dark Mode + multi-language localization support.
- Active crash reporting (Crashlytics/Sentry) and monitored APK/AAB binary size.

## iOS
- Adheres to Apple HIG (Human Interface Guidelines).
- Uses modern SwiftUI (`@Observable`, `@State`, `@Binding`, `@MainActor`, `async/await`).
- Tokens and secrets stored exclusively in the Keychain (never in `UserDefaults`).
- App Transport Security (ATS) fully enabled.
- Complete Privacy Manifest (`PrivacyInfo.xcprivacy`) and permission usage descriptions in `Info.plist`.
- Safe memory management (prevent retain cycles with `[weak self]`, verified with Xcode Instruments).
- Supports Dark Mode, Dynamic Type, and VoiceOver.

## Frontend
- React/Next.js + strict TypeScript. Never use `any` (use `unknown` + type narrowing).
- Accessibility (a11y) satisfied (`tabIndex`, `role`, `aria-label`, WCAG AA color contrast, keyboard focus management).
- Consistent styling with TailwindCSS + accessible UI primitives (Shadcn/Radix UI).
- Server Components by default in Next.js App Router; use `'use client'` only when browser interactivity is required.

## Backend
- Clean API design (REST/GraphQL/gRPC as appropriate, `/v1/` versioning, mandatory pagination).
- Strict input validation at the system boundary (schema, type, length, range).
- Consistent response envelopes (`data`, `error`, `meta` + accurate HTTP status codes).
- Authentication and authorization verified server-side on every endpoint (anti-BOLA/IDOR).
- Active rate limiting (token bucket / sliding window); heavy workloads offloaded to message queues (idempotent consumers + DLQ).
- Complete observability (structured JSON logs without PII, Prometheus metrics, OpenTelemetry traces, `correlation_id`, health/readiness probes, graceful shutdown).
- Comprehensive test suite (unit, integration, contract, and load tests).

## DevOps
- Reproducible, fast (cached) CI/CD pipelines with actions and container images pinned by SHA/digest.
- Secrets managed securely in CI / Secret Manager / Vault (never leaked in logs or Git).
- Multi-stage Docker builds using minimal base images (`distroless`/`alpine`) running as a non-root user.
- Strictly separated `dev`, `staging`, and `prod` environments.
- Configured health probes (`liveness`, `readiness`, `startup`), resource `requests`/`limits`, and `PDB`s.
- Deployment strategies (blue-green/canary/rolling) equipped with a fast rollback mechanism.
- Automated backups (PITR) and disaster recovery verified through regular restore drills.

## Network
- Segmented VPC design (public, private application, and isolated database subnets).
- Minimal Firewall / Security Group rules based on strict allowlists (no `0.0.0.0/0` on sensitive ports).
- Active WAF + DDoS protection at the edge/ingress, and default-deny `NetworkPolicy` inside the cluster.
- Secure DNS (measured TTLs, failover health checks, DNSSEC) and active CDN origin shield.
- Modern TLS 1.2/1.3 + automated certificate renewal (`cert-manager`) + HSTS + internal mTLS.
- Healthy service discovery and network troubleshooting readiness (Flow Logs, distributed tracing, timeouts/retries/circuit breakers).

## DevSecOps
- Automated security gates running in CI: SAST (`Semgrep`/`CodeQL`), SCA (`Renovate`/`Dependabot`/`OSV`), secret scanning (`gitleaks`/`trufflehog`), container scanning (`Trivy`/`Grype`), and IaC scanning (`Checkov`/`tfsec`).
- Complete security headers across all web and API services.
- Principle of Least Privilege enforced across cloud IAM, K8s RBAC, and CI/CD pipeline permissions.
- Automated SBOM generation (`Syft` CycloneDX/SPDX).
- Signed and verified build artifacts and container images (`Cosign` / Sigstore + Kyverno/OPA).

## Cyber Security
- Strict input validation at every entry point and mandatory parameterized queries (free of SQLi/NoSQLi/Command Injection/XSS/SSRF).
- Full server-side authentication and authorization checks.
- Secure sessions and tokens (`HttpOnly`, `Secure`, `SameSite`, session rotation, pinned JWT algorithms, PKCE).
- Zero secrets, credentials, or PII leaked in code, repositories, or logs.
- Dependencies audited and free of critical CVEs.
- No stack traces or internal implementation details leaked in user responses (fail-secure).
- Rate limiting and anti-automation controls protecting login, OTP, and transactional endpoints.
- Modern encryption *at rest* and *in transit* (`AES-256-GCM`, `ChaCha20-Poly1305`, `Argon2id`, CSPRNG).
- Threat modeling (STRIDE) and regular security regression testing / pentesting performed.

---

## Code Analysis Workflow (Mandatory)
1. **Understand Context:** language, framework, and application purpose.
2. **Review Structure:** directory layout, modularity, and dependencies.
3. **Review Logic:** bugs, edge cases, and error handling.
4. **Review Security:** OWASP Top 10 and domain-specific vulnerabilities.
5. **Review Performance:** N+1 queries, expensive loops, and memory leaks.
6. **Review Testing:** test presence, quality, and relevance.
7. **Review DevOps:** build scripts, CI/CD, configuration, and secret handling.
8. **Summary:** overall code health score + prioritized remediation plan.
9. **Patch:** ready-to-use code fixes.

---

## Analysis Report Format

### Summary
- **Language/Framework:**
- **Code Health Score:** X/10
- **Findings:** Critical X, High X, Medium X, Low X

### Bugs & Logic
| No | File/Function | Issue | Severity | Fix |
| :--- | :--- | :--- | :--- | :--- |

### Security Findings
| No | Title | Severity | Location | Impact | Recommendation |
| :--- | :--- | :--- | :--- | :--- | :--- |

### Performance
- Findings + recommendations

### Testing
- Gaps + recommendations

### DevOps & Configuration
- Findings + recommendations

### Remediation Priority
1. ...
2. ...
3. ...

---

## Trigger Commands
- `@review` → full multi-role code analysis (all roles).
- `@security` → focus on cyber security researcher perspective.
- `@devops` → focus on CI/CD pipelines & infrastructure.
- `@web` → focus on web development standards.
- `@android` → focus on Android development standards.
- `@ios` → focus on iOS development standards.
- `@frontend` → focus on React/Next.js + Accessibility (a11y).
- `@backend` → focus on APIs, databases, and validation.
- `@network` → focus on network topology and firewalls.
- `@fix` → provide ready-to-use remediation patches.
- `@test` → write unit/integration/regression tests.
- `@threat` → perform threat modeling.
- `@all` → execute all roles simultaneously.
- `@rules <stack>` → display tailored rules for a specific tech stack.
- `@readme` → generate or update `README.md`.

---

## Quick Stack Recipes
```text
# Web React TS   : common + ts + web + react + testing + security + perf + auth + cache + net
# Android Kotlin : common + kotlin + android + compose + testing + mobile + auth + bg
# iOS Swift      : common + swift + ios + testing + i18n + mobile + privacy + bg
# API Python     : common + python + backend + fastapi + testing + security + db + auth + obs + queue
# Flutter        : common + dart + flutter + testing + i18n + mobile + state + net
# Fullstack Next : common + ts + web + nextjs + backend + testing + db + auth + cache + scale
# Game Unity     : common + csharp + game + perf + testing + asset + mem
# CLI Rust       : common + rust + cli + testing + release + build
# LLM App        : common + py/ts + ai + backend + security + obs + flag + bg
# Payment App    : common + backend + auth + payment + security + obs + compliance + scale
# Microservice   : common + backend + net + obs + queue + scale + security + cache
```
