# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Added a Multi-AI Compatibility section to `README.md`, with native-format guidance, bridge examples, sync guidance, and manual setup notes.
- Added `CONTRIBUTING.md` with contribution steps, style guidance, and commit conventions.
- Added `SECURITY.md` with vulnerability reporting guidance, scope, and security checklists.
- Added `CODE_OF_CONDUCT.md`, adapted from Contributor Covenant version 2.1.
- Added this changelog.

### Changed

- Rewrote `README.md` in professional English and updated its project, licensing, contact, and documentation details.
- Updated the README folder tree and contribution guidance to reference the new documentation files.

### Fixed

- Removed incomplete License and Contact placeholders from `README.md`.

### Security

- Documented private vulnerability reporting and responsible disclosure in `SECURITY.md`.

## [1.0.0] - 2026-11-16

### Added

- Initial BuildFlow release with 10 modular rule files in `.agents/rules/`:
  - `01-common.md` — Common workflows and engineering practices.
  - `02-languages.md` — Language-specific guidance and additional language summaries.
  - `03-platforms.md` — Web, Android, iOS, backend, and other platform guidance.
  - `04-frameworks.md` — Framework guidance, including React, Next.js, Flutter, and FastAPI.
  - `05-devops.md` — DevOps, cloud, networking, and DevSecOps guidance.
  - `06-security.md` — Security guidance covering OWASP, injection, authentication, and cryptography.
  - `07-architecture.md` — Architecture and advanced engineering guidance.
  - `08-checklist.md` — Role-oriented checklists and trigger commands.
  - `09-readme-prompt.md` — Prompt guidance for generating or updating project READMEs.
  - `10-agents-md.md` — AI identity and agent behavior guidance.
- Added `LICENSE` with the MIT License and 2026 Hidayat / InfraSecID copyright.
- Added the initial project README.

---

## How to Update This Changelog

Add changes to the `[Unreleased]` section under the appropriate category:

- `Added` for new features or rules.
- `Changed` for updates to existing behavior or documentation.
- `Deprecated` for features scheduled for removal.
- `Removed` for features or files that have been deleted.
- `Fixed` for corrections and bug fixes.
- `Security` for security-related changes.

When preparing a release:

1. Move the relevant `[Unreleased]` entries to a new version section.
2. Add the release date.
3. Update the version according to Semantic Versioning.
4. Create a fresh `[Unreleased]` section at the top.

## Versioning

BuildFlow follows [Semantic Versioning](https://semver.org/):

- **MAJOR** for incompatible changes, such as removing a rule file or changing the frontmatter format.
- **MINOR** for backward-compatible additions, such as new rule files or sections.
- **PATCH** for backward-compatible fixes, such as typo corrections and clarifications.

---

**Build systems that outlive the hype.**
