# Security Policy

## Supported Versions

BuildFlow is a rules repository. Security updates apply to the latest version.

| Version | Supported |
|---|---|
| Latest | Yes |
| Older | No |

## Reporting a Vulnerability

We take security reports seriously. If you discover a vulnerability in BuildFlow, report it privately so it can be investigated before public disclosure.

### How to Report

**Do not open a public GitHub issue for a security vulnerability.**

Use one of these private channels:

- **GitHub Security Advisories:** [Report a vulnerability](https://github.com/InfraSecID/BuildFlow/security/advisories/new)
- **Email:** [TBD]
- **GitHub direct message:** [@InfraSecID](https://github.com/InfraSecID/)

### What to Include

Include as much of the following information as possible:

- A description of the vulnerability.
- Steps to reproduce the issue.
- The potential impact.
- A suggested fix, if available.
- Your contact information for follow-up.

### What to Expect

- We aim to acknowledge reports within 48 hours.
- We aim to provide a status update within 7 days.
- Fix timelines depend on severity:
  - Critical: 7 days.
  - High: 14 days.
  - Medium: 30 days.
  - Low: 90 days.

These are target timelines, not guarantees. Complex investigations or external dependencies may affect the schedule.

### Responsible Disclosure

1. The reporter submits the vulnerability privately.
2. The maintainer acknowledges and investigates the report.
3. A fix is developed and tested.
4. The fix is released.
5. Public disclosure takes place after 90 days, or sooner if the reporter and maintainer agree.

Researchers will be credited in release notes unless they prefer to remain anonymous.

## Scope

### In Scope

- Rule content that could encourage insecure implementation patterns.
- Outdated security recommendations, including outdated OWASP Top 10 references.
- Missing security guidance in rule files.
- Broken links to security resources.

### Out of Scope

- Vulnerabilities in third-party tools such as Antigravity, Cursor, or GitHub Copilot.
- Security issues in projects that use BuildFlow rules.
- Social engineering attacks.
- Denial-of-service attacks against third-party services.

## Security Best Practices for Contributors

When contributing security-related rules:

- Do not include API keys, tokens, passwords, or other secrets.
- Keep rules as documentation; do not add executable scripts to rule files.
- Do not add malicious or deceptive links.
- Follow the OWASP Top 10 2025 and other applicable, current standards.
- Review rules with an AI agent and verify important claims against authoritative sources.
- Review the security and maintenance status of any recommended third-party tools.

## Security Checklist for Users

Before using BuildFlow rules in a production project:

- [ ] Review the rules with your security team.
- [ ] Confirm that recommendations match your organization's standards and threat model.
- [ ] Do not treat AI-generated output as trusted without review.
- [ ] Review generated code before merging or deploying it.
- [ ] Keep your copy of the rules up to date.

## Contact

- **Security contact:** Hidayat ([@InfraSecID](https://github.com/InfraSecID/))
- **Email:** [TBD]
- **GitHub Security Advisories:** [Report a vulnerability](https://github.com/InfraSecID/BuildFlow/security/advisories/new)

## Acknowledgments

No public security researcher acknowledgments have been listed yet.

---

**Build systems that outlive the hype.**
