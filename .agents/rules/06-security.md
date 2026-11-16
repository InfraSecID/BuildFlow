---
trigger: always_on
---

# 06 — Security

## Golden Rules (Non-Negotiable)
- **NEVER** hardcode secrets, tokens, API keys, passwords, or PII in source code, tests, comments, or logs.
- **NEVER** weaken or disable security controls (disabling TLS verification, opening wildcard CORS `*` with credentials, bypassing auth/CSRF) just to make code "work".
- **NEVER** execute destructive commands (`rm -rf`, `DROP TABLE`, `TRUNCATE`, `git push --force`) without explicit user approval.
- **ALWAYS** validate and sanitize inputs at the system boundary. Treat all external inputs as *hostile*.
- **ALWAYS** use parameterized queries / prepared statements. String concatenation in DB queries or shell commands is strictly forbidden.
- **ALWAYS** enforce *Least Privilege*, *Defense in Depth*, and *Fail-Secure* defaults at every layer.

## OWASP Top 10 (2025)
- **A01:2025 Broken Access Control (including SSRF):** IDOR/BOLA, missing function-level authorization checks, CORS misconfigurations, path traversal, and **SSRF** (fetching user-supplied URLs without allowlist validation and internal IP/cloud metadata protection).
- **A02:2025 Security Misconfiguration (up from #5):** Active default credentials, debug mode/verbose error pages enabled in production, open directory listings, public cloud storage buckets, permissive CORS `*`, and missing security headers (`CSP`, `HSTS`, `X-Content-Type-Options`).
- **A03:2025 Software Supply Chain Failures (NEW):** Compromise across the software supply chain lifecycle: malicious or typosquatted dependencies, compromised build/CI/CD pipelines, unsigned/unverified artifact distribution, and unauthenticated auto-update mechanisms.
- **A04:2025 Cryptographic Failures (down from #2):** Sensitive data transmitted or stored in plaintext, weak or deprecated algorithms (`MD5`, `SHA-1`, `ECB`), unsalted passwords, hardcoded cryptographic keys, and misconfigured TLS.
- **A05:2025 Injection (down from #3):** SQLi, NoSQLi, OS Command Injection, LDAP, XPath, SSTI, XSS, and Header/Log Injection caused by concatenating untrusted input directly into an interpreter.
- **A06:2025 Insecure Design (down from #4):** Architectures built without threat modeling, business logic flaws, missing rate limits on sensitive workflows, and trusting client-side state or validation.
- **A07:2025 Authentication Failures:** Weak password policies, missing MFA, susceptibility to credential stuffing/brute force, session fixation, and insecure token/session lifecycle management (sessions not invalidated on logout).
- **A08:2025 Software or Data Integrity Failures:** Insecure deserialization, loading external plugins/CDN scripts without SRI or integrity checks, and accepting unsigned code or data modifications.
- **A09:2025 Logging & Alerting Failures (focused on alerting):** Failing to log critical security events, logging sensitive PII/secrets, and **lacking active alerting and real-time anomaly detection** to respond to ongoing attacks.
- **A10:2025 Mishandling of Exceptional Conditions (NEW):** Covers 24 CWEs related to poor error and exception handling: failing open (*fail-open*) during exceptions, logic flaws triggered by timeouts/out-of-memory/null states, unreleased resources on failure, and leaking stack traces or internal state in error responses.

## OWASP API Security Top 10 (2023)
- **API1:2023 Broken Object Level Authorization (BOLA):** Verify on every endpoint that the authenticated user owns or is authorized to access/modify the requested object ID.
- **API2:2023 Broken Authentication:** Secure token generation, storage, rotation, and revocation (JWT/OAuth2/API Keys); never pass tokens in URL query parameters.
- **API3:2023 Broken Object Property Level Authorization (BOPLA):** Combines *Excessive Data Exposure* (returning full database rows in JSON responses) and *Mass Assignment* (blindly binding all request body fields). Always use explicit request and response DTOs.
- **API4:2023 Unrestricted Resource Consumption:** Enforce payload size limits, pagination limits, GraphQL query depth/cost limits, CPU/memory quotas, execution timeouts, and per-user/IP/tenant rate limits.
- **API5:2023 Broken Function Level Authorization (BFLA):** Strictly verify roles and permissions for administrative, internal, and state-mutating HTTP methods (`POST`/`PUT`/`PATCH`/`DELETE`).
- **API6:2023 Unrestricted Access to Sensitive Business Flows:** Protect sensitive business workflows (checkout, coupon redemption, fund transfers, ticket purchases, OTP requests) against automated abuse, bots, and scalping.
- **API7:2023 Server-Side Request Forgery (SSRF):** Validate webhook URLs, avatar fetchers, URL imports, and third-party integrations using domain allowlists while blocking private IPs and cloud metadata endpoints.
- **API8:2023 Security Misconfiguration:** Harden CORS, TLS, allowed HTTP methods, and security headers, and disable unnecessary stack traces and API introspection in production.
- **API9:2023 Improper Inventory Management:** Maintain an accurate inventory of all API hosts and versions (`/v1`, `/v2`, `/beta`), and decommission unmaintained shadow or zombie APIs promptly.
- **API10:2023 Unsafe Consumption of APIs:** Never implicitly trust data returned from third-party APIs or upstream microservices; validate schemas, sanitize content, enforce timeouts, and cap response sizes.

## OWASP Mobile Top 10 (2024) & MASVS v2.0.0
- **OWASP Mobile Top 10:** `M1` Improper Credential Usage, `M2` Inadequate Supply Chain Security, `M3` Insecure Auth/Authz, `M4` Insufficient Input/Output Validation, `M5` Insecure Communication, `M6` Inadequate Privacy Controls, `M7` Insufficient Binary Protections, `M8` Security Misconfiguration, `M9` Insecure Data Storage, `M10` Insufficient Cryptography.
- **8 Control Groups of OWASP MASVS v2.0.0:**
  - **MASVS-STORAGE:** Secure on-device storage of sensitive data (Keystore/Keychain, encrypted local databases, preventing leaks via backups, logs, or clipboard).
  - **MASVS-CRYPTO:** Use of OS-standard cryptographic primitives, modern algorithms, and hardware-backed key management (TEE/Secure Enclave).
  - **MASVS-AUTH:** Strong authentication and authorization (biometrics cryptographically bound to Keystore/Keychain, short-lived tokens, step-up authentication).
  - **MASVS-NETWORK:** Encrypted network communication (TLS 1.2/1.3, ATS/Network Security Config, and Certificate Pinning for high-risk connections).
  - **MASVS-PLATFORM:** Secure interaction with the mobile OS platform (IPC, Intents, Deep Links/Universal Links, ContentProviders, and WebViews).
  - **MASVS-CODE:** Application code quality, strict input/output validation, safe error handling, and up-to-date dependencies/SDKs.
  - **MASVS-RESILIENCE:** Binary resilience against reverse engineering and tampering (R8/ProGuard obfuscation, root/jailbreak/Frida/debugger detection, Play Integrity / App Attest).
  - **MASVS-PRIVACY:** User data minimization, transparent permissions (contextual rationale & Privacy Manifest), and protection of device/user identifiers.

## OWASP Top 10 Privacy Risks
- **Over-Collection:** Collecting personal data (PII, precise location, contacts) beyond what is strictly necessary for the core business feature (*violating data minimization*).
- **Poor Consent Management:** Obtaining consent through manipulative *dark patterns*, vague terms, or failing to provide an easy way to withdraw consent.
- **Weak Data Retention & Deletion:** Retaining user data indefinitely without automated retention/purging schedules, or failing to completely erase user records upon a *Right to be Forgotten* request.
- **Excessive Data Sharing:** Sharing or leaking PII to analytics SDKs, ad networks, or third parties without proper anonymization/pseudonymization and a valid legal basis.
- **Inadequate Identity & Access Protection:** Insufficient internal access controls, missing audit logs, and lack of masking/tokenization, allowing unauthorized parties to view or correlate (*linkability*) sensitive user data.

## Injection Variants
- **SQLi:** Enforce parameterized queries / prepared statements, least-privilege DB accounts, and strict allowlists for dynamic `ORDER BY` column names.
- **NoSQLi:** Strictly validate input types (reject injected objects/operators such as `$where`, `$ne`, `$gt`, `$regex`).
- **Command Injection:** Avoid invoking a shell (`sh -c`, `exec`); use native language APIs or `execFile` with separated argument arrays and strict allowlists.
- **SSTI:** Never concatenate user input into template strings; pass user data exclusively as sandboxed template context variables.
- **LDAP / XPath Injection:** Escape special control characters and use parameterized filters/queries.
- **Log & Header/CRLF Injection:** Strip or encode newline characters (`\r`, `\n`), validate header values, and use structured JSON logging.
- **Insecure Deserialization:** Never deserialize native binary formats (`pickle`, `ObjectInputStream`, `unserialize`, `BinaryFormatter`) from untrusted sources; use JSON/Protobuf with strict schema validation and cryptographic signatures.
- **XXE:** Disable external DTDs and external entities (`disallow-doctype-decl`) across all XML, SOAP, SAML, SVG, and DOCX parsers.
- **Prototype Pollution:** Validate JSON keys (reject `__proto__`, `constructor`, `prototype`), use `Map` or `Object.create(null)`, and freeze configuration objects.
- **GraphQL & SpEL/OGNL:** Enforce query depth and complexity limits, disable introspection in production, and never evaluate (`eval`) user-supplied SpEL/OGNL expressions.

## Client-Side Security
- **XSS:** Contextually encode outputs, enforce a strict CSP (nonce/hash-based), sanitize HTML with `DOMPurify`, and avoid `innerHTML` / `dangerouslySetInnerHTML`.
- **CSRF:** Use anti-CSRF tokens, `SameSite=Lax`/`Strict` cookies, and verify `Origin`/`Referer` headers.
- **Clickjacking:** Set `Content-Security-Policy: frame-ancestors 'none'` (or `'self'`) and `X-Frame-Options: DENY`.
- **CORS & Open Redirect:** Use exact origin allowlists (never reflect arbitrary origins with credentials enabled), and validate redirect URLs against a strict path/domain allowlist.
- **postMessage & Frontend Supply Chain:** Always verify `event.origin` in `postMessage` listeners, enforce **SRI** (`integrity`) attributes on external scripts, and guard against dependency confusion and CDN compromise.

## Server-Side Security
- **SSRF:** Enforce `https`-only schemes, validate against domain allowlists, resolve DNS and block internal/loopback/cloud metadata IPs (`169.254.169.254`), and disable automatic redirect following.
- **Path Traversal & File Upload:** Canonicalize file paths and verify directory prefixes. Validate uploaded files by magic bytes, MIME type, and extension allowlist; cap file sizes; rename files to random UUIDs; scan for malware; store in object storage outside the webroot; and serve downloads from a sandboxed domain with `X-Content-Type-Options: nosniff`.
- **RCE & Deserialization:** Avoid `eval`/`exec`, and run untrusted or high-risk processing inside minimally privileged sandboxes/containers.
- **Race Conditions & Business Logic Flaws:** Prevent TOCTOU flaws using atomic DB transactions, row-level locks (`SELECT ... FOR UPDATE`), or distributed locks + idempotency keys. Re-validate prices, discounts, positive quantities, and workflow state transitions on the server.
- **Mass Assignment & HTTP Smuggling/Cache Poisoning:** Enforce explicit allowlist DTOs, normalize `Content-Length`/`Transfer-Encoding` parsing at the reverse proxy, and validate the `Host` header.

## Auth & Session Management
- **Password & MFA:** Hash passwords with `Argon2id` or `bcrypt` + unique salts, enforce strong password policies, and check against breached password corpora (HIBP). Support MFA (`TOTP` / `WebAuthn` Passkeys) and store backup codes hashed.
- **Sessions:** Set `HttpOnly` + `Secure` + `SameSite` flags on session cookies (use the `__Host-` prefix where applicable), rotate session IDs upon login and privilege elevation, enforce both idle and absolute timeouts, and invalidate sessions server-side on logout.
- **JWT & OAuth/OIDC:** Pin accepted JWT algorithms on the server (reject `alg: none` and prevent key confusion), validate signature + `exp` + `iss` + `aud`, use short-lived access tokens + refresh token rotation. In OAuth/OIDC, always enforce **PKCE**, `state`, `nonce`, and exact `redirect_uri` matching.
- **Recovery & Privilege Escalation:** Generate password reset tokens using a CSPRNG, make them single-use with short expiration, return uniform response messages and timing (preventing user enumeration), enforce account lockout/rate limits, and verify roles/ownership server-side.

## Cryptography
- **Encryption:** Use AEAD ciphers (`AES-256-GCM` or `ChaCha20-Poly1305`) with unique nonces; never use `ECB` or unauthenticated `CBC`.
- **Hashing & Randomness:** Use `SHA-256` / `SHA-3` / `BLAKE2` for data integrity and `Argon2id`/`bcrypt`/`scrypt` for passwords. Always use a **CSPRNG** for security-sensitive random values (never use `Math.random()`).
- **Key Management & TLS:** Store cryptographic keys in KMS / HSM / Vault / OS Keystores with scheduled rotation, and enforce modern TLS 1.2/1.3 + HSTS.

## Cloud & Infrastructure Security
- **IAM & Metadata:** Enforce least-privilege IAM policies (no wildcard `*`), require MFA on root/admin accounts, use OIDC/workload identity instead of static keys, and require **IMDSv2**.
- **Storage, Network & Compute:** Block public access on storage buckets (S3/GCS/Blob), store secrets in Secret Manager, and isolate workloads in private subnets protected by minimal Security Groups, WAF, and DDoS mitigation.
- **Containers, K8s & Serverless:** Use scanned, trusted base images running as non-root; harden K8s RBAC, `NetworkPolicy`, `PodSecurity` (`restricted`), and etcd Secret encryption; assign least-privilege IAM roles per serverless function.

## Supply Chain Security
- **Dependencies & Builds:** Pin dependency versions, commit lockfiles, audit licenses and CVEs, and prevent dependency confusion / typosquatting (use scoped packages and private registries).
- **SBOM, Signing & SLSA:** Generate SBOMs (`CycloneDX` / `SPDX`), sign artifacts and container images with **Sigstore / Cosign**, and produce **SLSA provenance** attestations to guarantee build integrity.
- **CI/CD & Repository:** Pin GitHub Actions to full commit SHAs, scope down `GITHUB_TOKEN` permissions, prevent secret leaks in build logs, and enforce signed commits, branch protection, and mandatory code reviews.

## Threat Modeling
- Apply **STRIDE** (Spoofing, Tampering, Repudiation, Information Disclosure, DoS, Elevation of Privilege), **DREAD**, **PASTA**, and **LINDDUN** (privacy threat modeling).
- Follow a systematic workflow: identify *Assets* → map *Data Flow Diagrams (DFD)* & *Trust Boundaries* → enumerate *Threats* & *Attack Surfaces* (endpoints, inputs, auth, dependencies, infra, third parties) → design *Mitigations* → *Verify* on every new feature.

## Security Testing
- **Automated Scanning:** Integrate SAST (`Semgrep`, `CodeQL`, `SonarQube`), DAST (`OWASP ZAP`, authorized `Burp Suite` / `Nuclei`), SCA (`Dependabot`, `Renovate`, `Snyk`, `OSV-Scanner`), Secret Scanning (`gitleaks`, `trufflehog`), and Container/IaC Scanning (`Trivy`, `tfsec`, `Checkov`).
- **Fuzzing:** Run continuous fuzzing against parsers and critical functions using **OSS-Fuzz**, **libFuzzer** / **AFL++** (C/C++), **Atheris** (Python), or **cargo-fuzz** (Rust).
- **Penetration Testing:** Conduct regular pentests with clearly defined **scoping**, written **Rules of Engagement (RoE)**, standardized **report templates** (CVSS/risk rating, PoC, remediation guidance), and post-fix verification *retests*.
- **Bug Bounty & Vulnerability Disclosure:** Publish `/.well-known/security.txt`, and operate bug bounty programs on established platforms (**HackerOne**, **Bugcrowd**, **Intigriti**) once security maturity is established, with clear **scope** (in-scope vs out-of-scope), *safe harbor*, and severity-based **reward tiers**.
- **Adversary Simulation & Regression:** Conduct **Red Team** (objective-based offensive), **Blue Team** (detection and defense), and **Purple Team** (collaborative detection engineering) exercises, and write automated **security regression tests** for every vulnerability fixed.

## Monitoring & Incident Response
- **Security Logging & Detection:** Log all security-relevant events (failed logins, access denials, role changes, admin actions, quota anomalies) and correlate them in a **SIEM** using **Sigma rules**, complemented by IDS/IPS, WAF, and EDR/eBPF sensors.
- **Incident Response (IR) Lifecycle:** Execute a structured *Detection → Containment → Eradication → Recovery → Lessons Learned* workflow.
- **Severity Matrix:** Classify incidents consistently from **SEV-1** (critical / active data breach / core production outage — immediate 24/7 response), **SEV-2** (high / major component impacted), **SEV-3** (medium / contained impact), to **SEV-4** (low / informational).
- **Communication Plan:** Establish pre-approved emergency communication channels for **internal stakeholders** (engineering, legal, executive leadership), **customers** (transparent status page updates and security advisories), and **regulators/authorities** (meeting mandatory breach notification windows such as 72 hours under GDPR / data protection laws).
- **Digital Forensics:** Maintain strict **chain of custody** (documenting who accessed evidence, when, and cryptographic hashes) and perform **artifact preservation** (memory/RAM snapshots, disk images, container state, and immutable network/audit logs) before terminating or rebuilding compromised hosts.
- **Lessons Learned & Vulnerability Management:** Conduct a blameless **post-mortem** using a standard template, track **action items** to completion, and enforce strict remediation SLAs based on CVE/finding severity.

## Compliance & Security Standards
- **OWASP ASVS 5.0:** Apply the **Application Security Verification Standard 5.0** (Level 1 for all applications, Level 2 for applications handling sensitive data, Level 3 for critical systems) and reference controls using the **`v5.0.0-<chapter>.<section>.<requirement>`** format (e.g., `v5.0.0-2.1.1`), alongside **OWASP MASVS v2.0.0** (mobile) and **OWASP SAMM** (maturity model).
- **NIST CSF 2.0:** Align cybersecurity governance with the 6 core functions of the **NIST Cybersecurity Framework 2.0**: **Govern (GV)**, **Identify (ID)**, **Protect (PR)**, **Detect (DE)**, **Respond (RS)**, and **Recover (RC)**.
- **CIS Benchmarks (Specific Hardening):** Apply industry-standard hardening benchmarks including the **CIS Docker Benchmark**, **CIS Kubernetes Benchmark** (`kube-bench`), **CIS AWS Foundations Benchmark**, **CIS PostgreSQL Benchmark**, **CIS NGINX Benchmark**, and **CIS GitHub Benchmark**.
- **SOC 2:** Satisfy and automate evidence collection across the 5 **Trust Services Criteria (TSC)**: *Security* (Common Criteria), *Availability*, *Processing Integrity*, *Confidentiality*, and *Privacy*.
- **ISO/IEC 27001:2022:** Maintain a risk-based *Information Security Management System (ISMS)* and map technical controls to **Annex A** (*Organizational, People, Physical, and Technological controls*).
- **PCI-DSS v4.0:** When storing, processing, or transmitting payment card data, isolate the Cardholder Data Environment (CDE) and comply with all **12 PCI-DSS Requirements** (network segmentation, secure configuration, protecting stored cardholder data/PAN, strong cryptography in transit, anti-malware, secure SDLC, need-to-know access control, strong authentication/MFA, physical security, logging/monitoring, regular security testing, and information security policies).
- **HIPAA:** For healthcare systems handling ePHI, comply with the **HIPAA Privacy Rule**, **Security Rule** (*Administrative, Physical, and Technical Safeguards*), and *Breach Notification Rule*.
- **GDPR & Data Protection Regulations (PDP):** Enforce *Privacy by Design & by Default*, conduct a **DPIA (*Data Protection Impact Assessment*)** for high-risk data processing, and provide automated workflows to fulfill **Data Subject Requests (DSR)** (right of access, rectification, data portability, consent withdrawal, and erasure/anonymization).
