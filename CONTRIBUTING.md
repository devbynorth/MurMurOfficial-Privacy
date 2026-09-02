# Contributing to MurMurOfficial-Privacy

Thank you for considering contributing to MurMurOfficial-Privacy! We welcome all contributions that improve the project.

## How to Contribute

### Reporting Issues
- Use the [GitHub Issues Tracker](https://github.com/devbynorth/MurMurOfficial-Privacy/issues) to report problems or suggest new features.
- Clearly describe the issue or feature request with as much detail as possible.
- **For security vulnerabilities**, see [SECURITY.md](SECURITY.md) for responsible disclosure guidance.

### Submitting Pull Requests
- Fork the repository and create a new branch for your changes.
- Use clear and descriptive commit messages that explain *what* changed and *why*.
- Follow the project's privacy-first principles (see below).
- All pull requests must pass automated checks:
  - HTML validation (`htmlhint`)
  - Accessibility audit (`pa11y` WCAG2AA)
  - Content validation (required sections, metadata, security headers)
  - Workflow linting (`actionlint`)
  - Secret scanning (`gitleaks`)
  - Dependency audit (`npm audit`, `pip audit`)
- Submit a pull request with a detailed explanation of the changes.

### Privacy-First Principles

Any changes to this repository must preserve or strengthen the zero-data architecture:

1. **No data collection** — Never add code that collects, processes, or transmits user data.
2. **No external services** — Never add dependencies on third-party services, analytics, or CDNs.
3. **No cookies or tracking** — Never add cookies, localStorage, sessionStorage, or tracking pixels.
4. **No JavaScript execution** — The HTML linter (`htmlhint`) enforces `script-disabled: true`. Do not disable this.
5. **No forms or submissions** — Any user-facing form must not transmit data (CSP blocks `form-action`).
6. **Content-only changes** — Focus on improving the privacy policy documentation, not adding features.

### Coding Standards

- **HTML**: Valid HTML5, WCAG2AA compliant, no JavaScript
- **Styling**: Inline `<style>` only (CSP allows `style-src 'unsafe-inline'`)
- **Metadata**: Always include `charset="UTF-8"`, `viewport`, `description`, and `Content-Security-Policy`
- **External links**: Use `rel="noopener noreferrer"` for all external URLs
- **Dates**: Keep "Sist oppdatert" (updated date) in sync with meaningful content changes

### Testing Your Changes

Before submitting a pull request, run these checks locally:

```bash
# HTML validation
npx htmlhint@1.9.2 privacy.html

# Accessibility audit (requires browsers; optional)
npx pa11y@9.1.1 --standard WCAG2AA privacy.html

# Content validation
grep -r "example\.com" *.html && echo "ERROR: Placeholder content" || echo "✓ No placeholders"
grep -q "Content-Security-Policy" privacy.html && echo "✓ CSP present" || echo "ERROR: Missing CSP"
```

### Deployment Gate (Human Review Required)

This repository has **zero automated deployment**. Changes to `privacy.html`:

1. Must pass all CI checks (automated)
2. Require human review (pull request review)
3. Require human approval to merge to `main` (branch protection)
4. Trigger deployment to GitHub Pages only on merge to `main` (automated via CI)

**Maintainers must verify**:
- ✅ Content accuracy and legal compliance
- ✅ Privacy policy aligns with MurMur's actual behavior
- ✅ No data collection has been introduced
- ✅ No external services or tracking added
- ✅ All CI checks passed

### Security Considerations

- **Never commit secrets** — The repository is scanned by `gitleaks` in CI
- **Review dependencies** — All npm/pip packages are pinned and scanned for vulnerabilities
- **Follow CSP** — Content Security Policy headers enforce strict isolation
- **Preserve audit trail** — All changes are logged in git history with signed commits (when possible)

### Code of Conduct
- All contributors are expected to follow the [Code of Conduct](CODE_OF_CONDUCT.md) included in this project.

## File Structure

```
├── privacy.html              # The published privacy policy (Norwegian)
├── .github/workflows/        # CI/CD pipelines
│   ├── ci.yml              # HTML & content validation
│   ├── deploy.yml          # Safe deployment gate
│   ├── security-monitoring.yml
│   ├── accessibility.yml
│   ├── dependency-audit.yml
│   ├── workflow-lint.yml
│   └── validate-package.yml
├── .gitignore              # Prevent accidental commits
├── .htmlhintrc             # HTML validation rules
├── .pa11yrc.json           # Accessibility audit config
├── audit/                  # Security audits and documentation
├── SECURITY.md             # Vulnerability disclosure policy
├── CONTRIBUTING.md         # This file
├── CODE_OF_CONDUCT.md      # Community standards
├── README.md               # Project overview
└── LICENSE.txt             # MurMur proprietary license
```

## Questions?

- **General questions** → [GitHub Issues](https://github.com/devbynorth/MurMurOfficial-Privacy/issues)
- **Security concerns** → [SECURITY.md](SECURITY.md)
- **Code of Conduct issues** → See [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md)

## Thank You!
We appreciate your contributions and support! By contributing, you're helping maintain MurMur's commitment to privacy and trust.