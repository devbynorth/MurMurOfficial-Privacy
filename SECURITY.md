# Security Policy for MurMurOfficial-Privacy

## Reporting a Vulnerability

If you discover a security vulnerability in this repository, please report it responsibly via **GitHub Security Advisories** instead of creating a public issue.

1. Go to the repository's **Security** tab
2. Click **Report a vulnerability** or **Advisories**
3. Follow GitHub's guided process to disclose the vulnerability privately

Alternatively, you can contact the maintainers via the [GitHub Issues](https://github.com/devbynorth/MurMurOfficial-Privacy/issues) tracker with a clear subject line: `[SECURITY]`.

**Do not** disclose the vulnerability publicly until the maintainers have had an opportunity to investigate and address it.

## Security Posture

This repository contains:
- **Static HTML content only** — no runtime, no server, no database, no backend
- **Zero data collection** — the privacy policy documents MurMur's zero-data architecture
- **No user input** — this site is read-only documentation
- **No secrets** — only GitHub's auto-provisioned `GITHUB_TOKEN` is used in CI

### Attack Surface

The primary security concerns in this repository relate to the **CI/CD supply chain**:

1. **GitHub Actions** — all actions are pinned to commit SHAs (not mutable tags)
2. **Dependencies** — npm and pip packages are version-pinned
3. **Secrets scanning** — gitleaks scans for accidental credential exposure
4. **Permissions** — workflows use least-privilege permissions (`contents: read` by default)
5. **Deployment** — GitHub Pages deployment requires explicit OIDC authentication

### Not in Scope

- Client-side vulnerabilities (XSS, CSRF, etc.) — there is no client-side code
- Server-side vulnerabilities — there is no server
- Data leaks — there is no user data
- Authentication/authorization bypass — there are no user accounts
- SQL injection — there is no database

## Security Controls

| Control | Status | Details |
|---------|--------|---------|
| Static analysis | ✅ | HTML validation (`htmlhint`) |
| Accessibility audit | ✅ | WCAG2AA compliance (`pa11y`) |
| Dependency audit | ✅ | npm/pip vulnerability scanning |
| Secret scanning | ✅ | gitleaks scans for leaked credentials |
| Workflow linting | ✅ | `actionlint` validates GitHub Actions syntax |
| Code security | ✅ | CodeQL analysis (workflow files only) |
| Content validation | ✅ | Required sections, metadata, CSP headers checked |
| Deploy safety | ✅ | Validation gates before production deployment |

## Content Security Policy

The privacy policy uses a strict Content Security Policy:

```
Content-Security-Policy: default-src 'none'; style-src 'unsafe-inline'; form-action 'none'; base-uri 'none';
```

This policy:
- **Denies by default** — no resources load unless explicitly allowed
- **Allows inline styles only** — CSS is embedded in the HTML
- **Blocks form submissions** — no external form handling
- **Restricts base URLs** — prevents `<base>` tag injection

## Dependencies

| Tool | Purpose | Version | Vulnerability Scanning |
|------|---------|---------|------------------------|
| `htmlhint` | HTML validation | 1.9.2 | npm audit |
| `pa11y` | Accessibility audit | 9.1.1 | npm audit |
| `npm-audit-html` | Dependency reporting | 1.5.0 | npm audit |
| `actionlint` | Workflow linting | v1.7.12 | GitHub Actions updates |
| `gitleaks` | Secret scanning | v2 | GitHub Actions updates |
| `safety` | Python deps audit | 3.8.1 | pip audit |

All dependencies are version-pinned and updated via Dependabot.

## Continuous Integration

All workflows:
- Run on every push to `main` and all pull requests
- Validate content before deployment
- Fail closed on security violations
- Use minimal permissions (RBAC via `permissions:` blocks)
- Pin actions to commit SHAs

**No deployment occurs without passing all validation gates.**

## Compliance

This repository and the MurMur privacy policy aim to comply with:
- **GDPR** — zero data collection = full compliance
- **CCPA** — zero data collection = zero obligations
- **COPPA** — zero data collection from children = safe for all ages
- **ePrivacy Directive** — no cookies, no tracking

See [`privacy.html`](privacy.html) for the full privacy policy (Norwegian).

## Version History

The `audit/` directory contains dated security audits:
- `06-sikkerhet.md` (2026-07-29) — comprehensive security assessment

Security findings are tracked in pull requests and issues with the `[SECURITY]` label.

## Questions?

- **Privacy questions** → [GitHub Issues](https://github.com/devbynorth/MurMurOfficial-Privacy/issues)
- **Security vulnerabilities** → Use GitHub Security Advisories (private disclosure)
- **Contributing** → See [CONTRIBUTING.md](CONTRIBUTING.md)

---

**Last updated:** 2026-09-02
