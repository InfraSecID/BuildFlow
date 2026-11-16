# Contributing to BuildFlow

Thank you for considering a contribution to BuildFlow. Your feedback and improvements help keep the rules useful, accurate, and reusable.

## 📋 Table of Contents

- [Code of Conduct](#-code-of-conduct)
- [How Can I Contribute?](#-how-can-i-contribute)
- [Development Setup](#-development-setup)
- [Pull Request Process](#-pull-request-process)
- [Style Guide](#-style-guide)
- [Commit Message Convention](#-commit-message-convention)
- [Reporting Bugs](#-reporting-bugs)
- [Suggesting Features](#-suggesting-features)
- [Contact](#-contact)

## 🤝 Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md). All participants are expected to uphold its standards. Report concerns to [@InfraSecID](https://github.com/InfraSecID/).

## 💡 How Can I Contribute?

### Adding New Rules

1. Fork the repository.
2. Create a focused branch, such as `git checkout -b feature/new-rule`.
3. Add a Markdown file in `.agents/rules/` with YAML frontmatter:

   ```yaml
   ---
   trigger: always_on
   ---
   ```

4. Follow the existing format; use `01-common.md` as a reference.
5. Update `README.md` if the change affects project documentation.
6. Commit and push your branch.
7. Open a pull request.

### Improving Existing Rules

1. Fork the repository.
2. Create a focused branch, such as `git checkout -b improve/rule-name`.
3. Edit the relevant rule file.
4. Review the change with an AI agent when practical, and verify any technical claims.
5. Commit and push your branch.
6. Open a pull request.

### Translating Rules

The source rules are maintained in English. If you contribute a translation:

1. Create a `translations/` directory.
2. Add a language subdirectory, such as `id/`, `es/`, or `ja/`.
3. Preserve the source file structure and identify the translated language.
4. Update the README to document the available translation.

Do not replace or silently diverge from the English source rules.

## 🛠️ Development Setup

```bash
git clone https://github.com/InfraSecID/BuildFlow.git
cd BuildFlow
```

No build step is required; the rules are Markdown files. Edit the relevant files and review the result with your preferred AI agent.

## 🔄 Pull Request Process

1. Describe the problem and summarize the proposed change.
2. Update `README.md` when the change affects project usage or structure.
3. Update `CHANGELOG.md` if one is present and the change warrants an entry.
4. Verify that links, examples, and technical claims are accurate.
5. Include a clear pull request description and any relevant context.
6. A maintainer must approve the pull request before it is merged.

Before submitting, confirm that your pull request:

- [ ] Follows the existing Markdown and rule-file format.
- [ ] Uses professional English.
- [ ] Keeps each changed file below 48 KB.
- [ ] Has been reviewed with an AI agent when practical.
- [ ] Includes a clear description of the change.
- [ ] Contains no secrets or sensitive data.

## 🎨 Style Guide

### Markdown Rules

- Use YAML frontmatter with `trigger: always_on` or `trigger: model_decision` when appropriate.
- Use `##` for main sections and `###` for subsections.
- Use bullet lists for unordered items and numbered lists for sequential steps.
- Use fenced code blocks for commands, configuration, and code.
- Use tables for concise comparisons.
- Keep lines under 120 characters where practical.

### Naming

- Use `kebab-case.md` for rule filenames, such as `01-common.md`.
- Use `# NN — Section Name` for rule titles, such as `# 01 — Common & Workflows`.
- Use Title Case for section headings, such as `## Error Handling & Logging`.

### Language

- Write documentation in professional, natural English.
- Use clear, direct wording.
- Keep terminology consistent across rules.

## 📝 Commit Message Convention

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```text
<type>(<scope>): <description>

[optional body]

[optional footer]
```

Common types:

- `feat`: Add a feature or rule.
- `fix`: Correct an issue.
- `docs`: Update documentation.
- `refactor`: Restructure content without changing its intent.
- `test`: Add or update validation.
- `chore`: Perform maintenance.

Examples:

- `feat(rules): add Rust language guidance`
- `fix(security): correct OWASP Top 10 2025 mapping`
- `docs(readme): add multi-AI compatibility section`

## 🐛 Reporting Bugs

Before opening a bug report, search the [existing issues](https://github.com/InfraSecID/BuildFlow/issues). Include:

- A clear, descriptive title.
- The exact steps needed to reproduce the issue.
- Specific examples or affected rule files.
- The observed behavior and expected behavior.
- Your environment, including OS and AI agent versions when relevant.
- Screenshots or logs when they help explain the issue; remove secrets first.

## 💡 Suggesting Features

Submit feature suggestions through [GitHub Issues](https://github.com/InfraSecID/BuildFlow/issues). Include:

- A clear summary of the proposed feature.
- The problem it addresses.
- A step-by-step description or examples.
- Why it would be useful to BuildFlow contributors or users.

## 📬 Contact

- **Maintainer:** Hidayat ([@InfraSecID](https://github.com/InfraSecID/))
- **Repository:** [InfraSecID/BuildFlow](https://github.com/InfraSecID/BuildFlow)
- **Issues:** [GitHub Issues](https://github.com/InfraSecID/BuildFlow/issues)
- **Discussions:** [GitHub Discussions](https://github.com/InfraSecID/BuildFlow/discussions)

---

Thank you for contributing to BuildFlow.
