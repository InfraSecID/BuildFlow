# BuildFlow

> A collection of modular Markdown rules to help AI coding agents follow engineering best practices across languages, platforms, architecture, DevOps, and security.

## Badges

[![CI](https://img.shields.io/badge/CI-not_configured-lightgrey)]()
[![Coverage](https://img.shields.io/badge/coverage-not_available-lightgrey)]()
[![License](https://img.shields.io/badge/license-MIT-blue)]()
[![Version](https://img.shields.io/badge/version-not_released-lightgrey)]()

## Table of Contents

1. [BuildFlow](#buildflow)
2. [Badges](#badges)
3. [Table of Contents](#table-of-contents)
4. [About](#about)
5. [Features](#features)
6. [Tech Stack](#tech-stack)
7. [Architecture](#architecture)
8. [Prerequisites](#prerequisites)
9. [Installation](#installation)
10. [Configuration](#configuration)
11. [Multi-AI Compatibility](#-multi-ai-compatibility)
12. [Usage](#usage)
13. [Testing](#testing)
14. [Build & Deploy](#build--deploy)
15. [Folder Structure](#folder-structure)
16. [Security](#security)
17. [Contributing](#contributing)
18. [License](#license)
19. [Contact](#contact)
20. [Acknowledgments](#acknowledgments)
21. [Footer](#footer)

## About

BuildFlow is a reusable collection of 10 modular Markdown rule files in `.agents/rules/`. It helps AI coding agents and development teams apply consistent engineering practices without treating the rules as application code or an automatic loader.

The project is intended for developers, maintainers, and teams working across multiple stacks and roles. Its role-oriented guidance covers programming, web, Android, iOS, frontend, backend, DevOps, networking, DevSecOps, and cybersecurity. The rules address a common problem: engineering guidance can become fragmented across teams and projects. BuildFlow groups that guidance into focused files that teams can review and reuse.

The repository contains documentation, not a runnable application. Whether an agent loads or interprets the rule metadata depends on that agent and its configuration.

## Features

- **10 modular rules:** Focused guidance for common workflows, languages, platforms, frameworks, DevOps, security, architecture, role checklists, README authoring, and agent behavior.
- **Multi-role coverage:** Guidance for engineering and security roles, including programmer, frontend, backend, DevOps, network, DevSecOps, and cybersecurity workflows.
- **Always-on guidance:** Nine rule files use `trigger: always_on` metadata for general guidance.
- **Task-aware guidance:** `09-readme-prompt.md` uses `trigger: model_decision` metadata for task-specific README work.
- **Context-efficient structure:** Focused Markdown files help keep rule selection relevant and make individual topics easier to reuse.
- **Reusable across projects:** Copy `.agents/rules/` into a project that uses a compatible AI coding agent.
- **Multi-AI compatibility guidance:** Explains native instruction formats and bridge options for supported tools.
- **English-language documentation:** The rules are written in English for consistent use across teams.
- **Planned templates:** Reusable project templates are planned; they are not included yet.
- **Planned dashboard:** A rules management dashboard is planned; it is not included yet.

## Tech Stack

| Layer | Technology / Status |
|---|---|
| Documentation format | Markdown |
| Rule metadata | YAML frontmatter |
| Version control | Git |
| Application runtime | None; this is a documentation repository |
| Application dependencies | None |
| Automated tests and CI | Not configured |
| Referenced technologies | Examples in the rules only; they are not BuildFlow dependencies |

## Architecture

BuildFlow has no application runtime, services, or configured rule-loading mechanism. Its repository structure organizes guidance for use by a developer and a compatible AI agent:

```text
Developer or team
       |
       | selects relevant guidance
       v
AI coding agent <---- reads ---- .agents/rules/*.md
       |
       v
Project work guided by the selected rules
```

The `trigger` values are metadata. Their behavior depends on the host agent; the repository itself does not implement automatic loading.

## Prerequisites

- **Git** to clone or share the repository.
- **An AI coding agent** that can read project Markdown rules. Examples include:
  - [Google Antigravity and the `agy` CLI](https://antigravity.google/product/antigravity-cli)
  - [Cursor](https://cursor.com/)
  - [Windsurf](https://windsurf.com/)
  - [GitHub Copilot](https://github.com/features/copilot)
- A text editor or IDE to inspect and adapt the rules.

Agent-specific setup and support for `.agents/rules/` vary. Check the documentation for your selected tool.

## Installation

Clone BuildFlow, copy its rules into a target project, then open that project with a compatible agent:

```bash
git clone https://github.com/InfraSecID/BuildFlow.git
cp -R BuildFlow/.agents /path/to/your-project/
cd /path/to/your-project
agy
```

The `agy` command starts the Antigravity CLI when it is installed. For Cursor, Windsurf, GitHub Copilot, or another agent, open the target project using that tool's documented workflow.

## Configuration

Each rule file uses YAML frontmatter to declare its trigger metadata. For example:

```yaml
---
trigger: always_on
---
```

Task-specific guidance can use:

```yaml
---
trigger: model_decision
description: Prompt to generate or update README.md
---
```

In this repository, nine files use `always_on` and `09-readme-prompt.md` uses `model_decision`. The values indicate different intended activation scopes: general guidance versus guidance selected for relevant tasks. The host agent determines how, or whether, these values affect rule loading.

No application environment variables or runtime settings are defined.

## 🤖 Multi-AI Compatibility

BuildFlow keeps its source rules in `.agents/rules/`. AI tools differ in their native instruction files and in how they interpret frontmatter. Use the table below as a starting point, then verify behavior against the documentation for your tool and version.

| AI / Tool | Native `AGENTS.md` | Native `.agents/rules/` | Notes |
|---|---|---|---|
| [Antigravity / agy](https://antigravity.google/product/antigravity-cli) | Yes | Intended target; verify version | Check current Antigravity documentation for rule discovery and frontmatter behavior. |
| [Cursor](https://cursor.com/docs/rules) | Yes | No | Native project rules use `.cursor/rules/*.mdc`; `AGENTS.md` is also supported. |
| [GitHub Copilot](https://docs.github.com/en/copilot/concepts/prompting-and-context/custom-instructions) | Yes, for supported agent surfaces | No | Repository instructions can also use `.github/copilot-instructions.md`; support varies by client and feature. |
| [Claude Code](https://code.claude.com/docs/en/memory) | Yes | No | Reads `AGENTS.md` as project instructions; `CLAUDE.md` also supports imports. |
| [Windsurf / Devin Local](https://docs.devin.ai/desktop/devin-local/customizations.md) | Yes | No | Current Devin Local rules use `.devin/rules/*.md`; legacy Windsurf rule paths are imported. |
| [Gemini CLI](https://geminicli.com/docs/cli/gemini-md/) | Via `GEMINI.md` import or configuration | No | Uses `GEMINI.md` by default and supports `@` imports. |
| [Codex](https://developers.openai.com/codex/guides/agents-md) | Yes | No | Reads `AGENTS.md` instructions. |

Native file discovery does not guarantee identical interpretation of `trigger` frontmatter. In particular, verify Antigravity's `.agents/rules/` discovery behavior in the version you use.

### Recommended Approach: Bridge + Sync

Use tool-specific instruction files to point agents to a shared `AGENTS.md`, then synchronize those files when a tool does not support the shared format directly.

#### Step 1 — Create Bridge Files

Create a root-level `AGENTS.md` that tells compatible agents where the source rules live:

```markdown
# BuildFlow Rules

Read and follow the relevant guidance in `.agents/rules/` for each task.
The files in `.agents/rules/` are the source of truth.
```

Add tool-specific files when required:

- **Claude Code:** `CLAUDE.md` can contain `@AGENTS.md`. Claude Code also reads `AGENTS.md` directly.
- **Cursor:** Create `.cursor/rules/buildflow.mdc` and reference `AGENTS.md` and the relevant source rules.
- **GitHub Copilot:** Create `.github/copilot-instructions.md` with a pointer to `AGENTS.md` and relevant files in `.agents/rules/`.
- **Gemini CLI:** Create `GEMINI.md` with `@./AGENTS.md`.
- **Windsurf / Devin Local:** Use `AGENTS.md` or the current `.devin/rules/` format.
- **Codex:** Place `AGENTS.md` at the project root.

Example symlinks for tools that support the referenced files:

```bash
ln -s AGENTS.md CLAUDE.md
ln -s AGENTS.md GEMINI.md
```

Example Cursor rule:

```markdown
---
description: BuildFlow project guidance
globs: "**/*"
alwaysApply: true
---

Follow the relevant guidance in AGENTS.md and `.agents/rules/`.
```

Example GitHub Copilot instructions:

```markdown
Read AGENTS.md and follow the relevant guidance in `.agents/rules/`.
```

Import syntax, symlink behavior, and rule loading differ by tool. Confirm each target file is actually loaded before relying on it.

#### Step 2 — Use a Sync Tool

For automated generation of tool-specific instruction files, [agnostic-ai](https://github.com/Chemaclass/agnostic-ai) is a third-party option. It is not a BuildFlow dependency. Review its documentation and generated changes before committing them.

```bash
npm install -g agnostic-ai
agnostic-ai init
agnostic-ai sync --plan
agnostic-ai sync
agnostic-ai sync --check
```

Initialize the tool and choose its targets before syncing. Do not assume it imports `.agents/rules/` or preserves existing instruction files without checking its plan.

#### Step 3 — Manual Copy

- **Antigravity / agy:** Use `.agents/rules/` if supported by your installed version.
- **Cursor, Windsurf / Devin Local, and Codex:** Use `AGENTS.md` or the tool's documented native rule format.
- **Claude Code:** Use `AGENTS.md` directly, or a `CLAUDE.md` file that imports it.
- **GitHub Copilot:** Use `AGENTS.md` on supported agent surfaces or `.github/copilot-instructions.md`.
- **Gemini CLI:** Use `GEMINI.md`, optionally importing `AGENTS.md`.

BuildFlow provides one source collection, not a universal loader. For reliable results, use each tool's documented format and confirm that the intended rules appear in its active context.

## Usage

Copy the rules into your project, then ask your compatible agent to follow the relevant guidance. The role and task triggers documented in `08-checklist.md` include:

| Trigger | Intended focus |
|---|---|
| `@review` | Multi-role code analysis |
| `@security` | Cybersecurity review |
| `@devops` | CI/CD and infrastructure |
| `@web` | Web development |
| `@backend` | APIs, databases, and validation |
| `@threat` | Threat modeling |
| `@rules <stack>` | Rules tailored to a technology stack |
| `@readme` | Generate or update a project README |

Example prompts:

- `@review Check this change for correctness, security, and test coverage.`
- `@rules Next.js Summarize the relevant stack guidance.`
- `@readme Review the project files and update its README without inventing features.`

Trigger handling varies by agent. If a trigger is not recognized, provide the rule file or relevant guidance explicitly.

## Testing

BuildFlow does not currently include an automated test suite. You can verify the rule file count and trigger metadata with:

```bash
test "$(find .agents/rules -maxdepth 1 -type f -name '*.md' | wc -l)" -eq 10
grep -q '^trigger: always_on$' .agents/rules/01-common.md
grep -q '^trigger: model_decision$' .agents/rules/09-readme-prompt.md
```

Review rule changes manually for accuracy, consistency, and compatibility with the intended agent.

## Build & Deploy

There is no build or deployment pipeline. To publish changes to the configured GitHub remote:

```bash
git add README.md CONTRIBUTING.md SECURITY.md CODE_OF_CONDUCT.md CHANGELOG.md LICENSE .agents/rules/
git commit -m "docs: update BuildFlow guidance"
git push
```

To create a ZIP archive from the current commit:

```bash
git archive --format=zip --output=BuildFlow.zip HEAD
```

## Folder Structure

```text
BuildFlow/
├── .agents/
│   └── rules/
│       ├── 01-common.md
│       ├── 02-languages.md
│       ├── 03-platforms.md
│       ├── 04-frameworks.md
│       ├── 05-devops.md
│       ├── 06-security.md
│       ├── 07-architecture.md
│       ├── 08-checklist.md
│       ├── 09-readme-prompt.md
│       └── 10-agents-md.md
├── CHANGELOG.md
├── CODE_OF_CONDUCT.md
├── CONTRIBUTING.md
├── LICENSE
├── README.md
└── SECURITY.md
```

## Security

BuildFlow provides security guidance; it does not enforce security controls in adopting projects. See [SECURITY.md](SECURITY.md) for vulnerability reporting instructions.

- Never add credentials, tokens, or sensitive data to rules or examples.
- Treat rule content as guidance and validate recommendations against the project's threat model.
- Verify security controls in the actual implementation and CI/CD pipeline.
- Do not assume that a technology or control mentioned in a rule is configured in your project.

Before sharing changes:

- [ ] Check for secrets and sensitive information.
- [ ] Review security recommendations for accuracy and context.
- [ ] Confirm that examples do not imply unimplemented protections.

## Contributing

Contributions to the rules and documentation are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for the suggested workflow and contribution standards.

1. Fork the repository and create a focused branch.
2. Edit the relevant Markdown rule or documentation.
3. Review the change for clarity, consistency, and technical accuracy.
4. Check for secrets and unsupported claims.
5. Open a pull request with a concise description of the change.

Contribution checklist:

- [ ] Keep changes focused on the relevant rule or documentation.
- [ ] Preserve Markdown structure and valid links.
- [ ] Verify that examples and claims match the repository.
- [ ] Explain the motivation and impact in the pull request.

## License

MIT License — see [LICENSE](LICENSE).

## Contact

- **Maintainer:** Hidayat ([@InfraSecID](https://github.com/InfraSecID/))
- **Repository:** [InfraSecID/BuildFlow](https://github.com/InfraSecID/BuildFlow)
- **GitHub:** [https://github.com/InfraSecID/](https://github.com/InfraSecID/)
- **Issues:** [GitHub Issues](https://github.com/InfraSecID/BuildFlow/issues)
- **Discussions:** [GitHub Discussions](https://github.com/InfraSecID/BuildFlow/discussions)

## Acknowledgments

Inspired by the `AGENTS.md` convention and built by InfraSecID.

## Footer

> Build systems that outlive the hype.
