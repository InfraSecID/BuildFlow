---
trigger: model_decision
description: Prompt to generate or update README.md following pro-expert multi-role standards
---

# 09 — Prompt to Generate/Update README.md

Read and follow all rules in this directory.

Task: Generate or update `README.md` at the project root according to pro-expert multi-role engineering standards (Security, Programmer, Android, iOS, Frontend, Backend, DevOps, Network, DevSecOps).

## Mandatory Steps
1. Analyze the project first — inspect the directory structure, `package.json` / `requirements.txt` / `go.mod` / `build.gradle` / `Podfile`, configuration files, and the rules in this directory. Never fabricate the tech stack.
2. Identify:
   - Project name & concise one-sentence description.
   - Key features (derived from actual code, routes, and UI).
   - Tech stack (frontend, backend, DB, cache, queue, DevOps, cloud).
   - Architecture (monolith/microservices/serverless) + ASCII architecture diagram.
   - Prerequisites (runtime versions, DB, required tools).
   - Installation & local execution steps.
   - Environment variables (from `.env.example` / config files).
   - Testing, build & deployment commands.
   - Directory structure (reflecting actual folders).
   - API documentation (from routes/Swagger/OpenAPI).
   - Security practices (implemented in the codebase).
   - Contribution guidelines, license, and contact info.
3. Write `README.md` using clean Markdown:
   - Badges (CI, coverage, license, version) — use placeholders if not yet configured.
   - Table of contents and `##` headings per section.
   - Markdown tables for the tech stack and environment variables.
   - Code blocks for installation, run, test, and build commands.
   - ASCII architecture diagram where relevant.
   - Pre-deployment security checklist.
   - Never hardcode secrets — use `[ ]` placeholders or environment variable references.
4. Preserve existing `README.md` sections that remain relevant — merge, refine, and fill in missing sections.
5. Upon completion, report:
   - Sections added or updated.
   - Assumptions made.
   - Related files that may need updating (`.env.example`, `CONTRIBUTING.md`, etc.).

## Strict Rules
- Never fabricate features or technologies that do not exist in the codebase.
- If specific information cannot be determined from the repo, write `[to be completed]` and note it in your summary report.
- Tone & Style: Professional, concise, technical English.
- Final Output: Complete `README.md` content + brief summary of changes.

Trigger: `@readme` → execute this workflow.
