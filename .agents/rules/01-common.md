---
trigger: always_on
---

# 01 — Common & Workflows

## Naming & Structure
- Files: `kebab-case`. Classes/Components/Types: `PascalCase`. Functions/variables/methods: `camelCase` (or `snake_case` where idiomatic, such as Python or Rust).
- Constants & Env Vars: `UPPER_SNAKE_CASE`. DB tables/columns: `snake_case`.
- Separate business logic (domain), UI (presentation), and the data layer (repository/infrastructure).
- One file = one primary responsibility (Single Responsibility Principle). Avoid god files/classes (> 300 lines).
- Public functions must include clear contract docstrings/comments (parameters, return values, and error conditions).

## Error Handling & Logging
- Never swallow errors (empty `catch {}` blocks or `_ = err` without an explicit, documented reason).
- Use custom/typed errors or error wrapping with context (`%w` in Go, `cause` in TS/JS/Java/Python).
- Separate user-facing error messages (clear, polite, safe, and free of internal technical details) from internal logs (complete with context and stack traces).
- Use structured logging (JSON) with appropriate log levels (`debug`, `info`, `warn`, `error`, `fatal`) and always include a `correlation_id` / `request_id`.
- Never log secrets, passwords, tokens, session IDs, or PII.
- Fail fast when configuration validation or critical invariants fail; use retry with backoff for transient network/I/O errors.

## Security Baseline
- Never hardcode secrets, API keys, tokens, passwords, or credentials in source code, tests, comments, or git history.
- Validate all inputs at the system boundary (schema, type, length, range, format) and sanitize/encode outputs. Treat all external inputs as hostile.
- Always use parameterized queries / prepared statements; never concatenate strings in SQL/NoSQL queries or OS commands.
- Enforce HTTPS/TLS for all external and sensitive network communications.
- Apply the Principle of Least Privilege across file access, database accounts, APIs, and application permissions.

## Performance & Resource Baseline
- Measure with a profiler or benchmark before introducing complex optimizations; avoid premature optimization.
- Prevent N+1 query issues in databases and APIs; use batching, eager loading, or appropriate joins.
- Always enforce pagination / limits on queries and endpoints that return collections.
- Always close resources (DB connections, file handles, network streams, timers, listeners) using `defer`, `with`, `using`, or `finally`.
- Never block the main thread, UI thread, or event loop with synchronous I/O or heavy CPU computation.

## Dependency Management
- Pin dependency versions explicitly and always commit lockfiles (`package-lock.json`, `pnpm-lock.yaml`, `go.sum`, `Cargo.lock`, `poetry.lock`, etc.).
- Evaluate bundle size, maintenance status, license compatibility (avoid copyleft/GPL in closed-source projects), and security track record before adding new dependencies.
- Do not add external libraries for trivial functionality that can be written in a few lines of standard code.
- Run dependency vulnerability audits (CVE/SCA) regularly and in CI pipelines.

## Environment & Configuration
- Inject all environment-specific configuration via environment variables rather than hardcoding values in code.
- Validate the presence and types of required environment variables at application startup (fail fast if required variables are missing or invalid).
- Strictly separate `development`, `staging`, and `production` configurations.
- Always provide and update `.env.example` whenever new configuration variables are introduced; never commit real `.env` files.

## Testing
- Business logic must have unit tests. Cross-component, database, and API flows must have integration tests.
- Critical user journeys must have E2E tests. Every bug fix must include a regression test to prevent recurrence.
- Use descriptive test names: `should_do_x_when_y` (or equivalent BDD/table-driven naming). Test behavior and contracts, not internal implementation details.
- Use contract tests, snapshot tests, load tests, or chaos tests as appropriate for the system.
- Test data must be isolated, deterministic, and idempotent. Fix flaky tests at the root cause rather than skipping them (`skip`/`ignore`).

## Git & Code Review
- Make small, atomic, focused commits using Conventional Commits: `feat|fix|chore|docs|refactor|test|perf|security(scope): clear concise message`.
- Use standard branch naming from `main`/`develop`: `feature/<name>`, `fix/<name>`, `chore/<name>`, `hotfix/<name>`.
- Never commit build/generated artifacts, large binaries, or secrets. Keep `.gitignore` comprehensive.
- Every Pull Request (PR) must pass code review and green CI checks (lint, typecheck, test, security scan).
- Code Review Checklist: logic correctness, error handling, edge cases (null/empty/boundary/concurrency), security, test coverage, and performance impact.

## Versioning & Release
- Follow Semantic Versioning (`SemVer`: `MAJOR.MINOR.PATCH`) for all releases and packages.
- Document notable changes in `CHANGELOG.md` (Added, Changed, Fixed, Security, Breaking Changes).
- Tag production releases with signed Git tags (`vX.Y.Z`) and always keep a verified rollback procedure ready.

## Documentation & Onboarding
- `README.md` must include: project overview, brief architecture, prerequisites, how to run (one-command setup), environment variables, and how to run tests.
- Record significant architectural decisions using ADRs (*Architecture Decision Records*).
- Provide `CONTRIBUTING.md`, a complete `.env.example`, and dev seed scripts to streamline developer onboarding.

## Definition of Done (DoD)
- Code is clean, follows naming conventions, and passes linters, formatters, and static type checks with zero warnings or errors.
- All relevant unit, integration, and regression tests pass (100% green).
- Edge cases, error handling, and structured logging are properly implemented.
- No hardcoded secrets, new security vulnerabilities (OWASP), or performance regressions are introduced.
- Documentation (`README.md`, docstrings, OpenAPI/schemas, `.env.example`) is updated to reflect the changes.

## AI Coding Specific
- Provide relevant file context specifically; avoid flooding the context window with unrelated repository files.
- State a concise implementation plan before making large or multi-file changes.
- Iterate incrementally: write/modify code → run tests/verification → fix any errors.
- Always perform a self-review for security, edge cases, error handling, and performance before delivering the final output.
- Store and adhere to project rules and architectural decisions in `.agents/rules/`.
