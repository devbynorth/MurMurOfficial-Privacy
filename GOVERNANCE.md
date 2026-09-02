# Governance and Change Approval Process

This document explains how changes are reviewed, approved, and deployed in MurMurOfficial-Privacy.

## Change Management Philosophy

**DENY BY DEFAULT** — All changes require explicit human approval before production deployment.

- ✅ Automated validation gates prevent most mistakes
- ✅ Human review ensures policy accuracy and legal compliance
- ❌ No automatic merging or deployment, even if all CI checks pass

## Change Types and Approval Requirements

### Type 1: Bug Fixes and Corrections
**Examples:** Typos, formatting errors, broken links, HTML validation failures

**Process:**
1. Author submits pull request with clear description
2. CI runs: HTML validation, accessibility audit, content validation
3. At least one maintainer reviews for correctness
4. Maintainer approves and merges to `main`
5. Automated deployment to GitHub Pages occurs
6. Change is live within 5 minutes

**Human Gate Decision Required:** ✅ **MAINTAINER APPROVAL**

---

### Type 2: Policy Content Updates
**Examples:** Privacy policy text changes, retention policy updates, new compliance sections

**Process:**
1. Author submits pull request with detailed explanation
2. Author must cite:
   - Why the change is necessary
   - How it affects MurMur's privacy commitment
   - Any legal/compliance requirements
3. CI runs: HTML validation, accessibility audit, content validation
4. **SECURITY REVIEW:** Maintainers verify:
   - ✅ Changes align with zero-data architecture
   - ✅ No data collection or processing has been introduced
   - ✅ No external services or tracking added
   - ✅ Content is legally accurate
   - ✅ Privacy policy remains current and truthful
5. **LEGAL REVIEW:** For GDPR/CCPA/COPPA changes, consider external legal counsel
6. At least two maintainers approve
7. Maintainers merge to `main`
8. Automated deployment to GitHub Pages occurs
9. Change is live within 5 minutes

**Human Gate Decision Required:** ✅ **SECURITY + MAINTAINER APPROVAL** (+ legal counsel if applicable)

---

### Type 3: CI/CD Configuration Changes
**Examples:** Workflow updates, dependency upgrades, action version bumps, permission scoping

**Process:**
1. Author submits pull request with clear description
2. Author must explain:
   - Why the change is necessary
   - How it affects build/deploy security
   - Any dependency vulnerabilities being patched
3. CI runs: workflow lint, action audit, schema validation
4. **SECURITY REVIEW:** At least one maintainer verifies:
   - ✅ No permissions have been broadened
   - ✅ No sensitive variables/secrets are exposed
   - ✅ Deploy gates are still fail-closed
   - ✅ All actions are SHA-pinned (not tag-pinned)
   - ✅ Dependencies are pinned to specific versions
5. At least one maintainer approves
6. Maintainers merge to `main`
7. Workflow changes take effect on next push/PR

**Human Gate Decision Required:** ✅ **SECURITY REVIEW + MAINTAINER APPROVAL**

---

### Type 4: Dependency Updates (Automated)
**Examples:** Dependabot PRs for action versions, npm package updates

**Process:**
1. Dependabot automatically submits PR (pinned version updates)
2. CI runs: full test suite
3. **AUTOMATED SECURITY SCAN:**
   - ✅ GitHub's Dependabot security advisory check
   - ✅ No known vulnerabilities in the new version
4. Maintainer reviews for:
   - ✅ No breaking changes
   - ✅ Version bump is reasonable (patch/minor/major)
5. Maintainer approves and squash-merges to `main`
6. Workflow changes take effect immediately

**Human Gate Decision Required:** ✅ **MAINTAINER REVIEW** (automated scanning + manual approval)

---

## Approval Roles

### Maintainer
- Can review and approve all change types
- Can merge pull requests to `main`
- Responsible for GitHub repository settings
- Responsible for branch protection rules

### Security Reviewer
- Specialized role for policy and CI security changes
- Verifies zero-data architecture is preserved
- Checks for exposure of secrets or permissions

### Legal Reviewer (External)
- Engaged for GDPR/CCPA/COPPA policy changes
- Verifies legal accuracy and compliance
- Optional for patch/correction-only updates

## Branch Protection Rules

The `main` branch has these protections enabled:

| Rule | Enabled | Purpose |
|------|---------|---------|
| Require pull request reviews before merging | ✅ | Enforce human review |
| Require code owners review | ✅ | Designated maintainers must approve |
| Require branches to be up to date | ✅ | Prevent merge conflicts |
| Require status checks to pass | ✅ | All CI must pass before merge |
| Require conversation resolution | ✅ | Address all feedback |
| Restrict who can push to matching branches | ✅ | Only maintainers can push |

**Consequence:** No pull request can merge without:
1. ✅ All CI checks passing
2. ✅ At least one maintainer approval
3. ✅ Branch up to date with `main`

---

## Deployment Process

### Trigger
- Merge to `main` branch (via pull request only)

### Deployment Pipeline
1. CI runs: full validation suite
   - HTML validation
   - Accessibility audit
   - Content validation
   - Secret scanning
   - Workflow linting
2. **Automated gates check:**
   - ✅ All CI passes
   - ✅ Build succeeds
3. GitHub Actions deploy job runs:
   - Copies HTML to GitHub Pages artifact
   - Uploads artifact to GitHub Pages
4. **Deployment gates check:**
   - ✅ Artifact upload succeeds
   - ✅ DNS resolves correctly
5. Deployment complete → Live on GitHub Pages

### Deployment Verification
- Check https://devbynorth.github.io/MurMurOfficial-Privacy/privacy.html
- Verify last-updated date matches expected change
- Spot-check content for accuracy

### Rollback
**If deployment breaks the site:**
1. Identify the breaking change
2. Revert the commit: `git revert <COMMIT_SHA>`
3. Merge revert to `main`
4. Site rolls back to previous version automatically

---

## Audit Trail

All changes are recorded in:
- **Git history:** https://github.com/devbynorth/MurMurOfficial-Privacy/commits/main
- **PR history:** https://github.com/devbynorth/MurMurOfficial-Privacy/pulls?state=closed
- **Security audit:** `/audit/06-sikkerhet.md`
- **CI logs:** GitHub Actions workflow logs (visible to maintainers)

**Immutability guarantee:** Git history is append-only. No force-pushes or commits rewrites are permitted.

---

## Escalation Path

**If a maintainer disagrees with a change:**

1. Request changes in the PR review
2. Discuss in PR comments
3. If unresolved, escalate to:
   - Project lead (@devbynorth) for final decision
   - Code of Conduct review if behavioral issues arise

**If a security issue is discovered post-deployment:**

1. Report via [SECURITY.md](SECURITY.md) process
2. Create private security advisory (GitHub)
3. Notify affected parties privately
4. Fix and deploy patch
5. Publish security advisory publicly (if appropriate)

---

## Change Checklist

Before submitting a pull request, ensure:

- [ ] Change is necessary and aligns with project goals
- [ ] PR title is clear and descriptive
- [ ] PR description explains **what** and **why**
- [ ] All CI checks pass locally (if possible)
- [ ] No new secrets/credentials have been added
- [ ] No data collection/processing has been introduced
- [ ] No external services or tracking added
- [ ] Linked related issues (if applicable)
- [ ] Privacy policy date updated (if content changed)

Before approving/merging:

- [ ] All CI checks pass
- [ ] Code review feedback addressed
- [ ] Merge is via pull request (not force-push)
- [ ] Branch is up to date with `main`
- [ ] Approval is explicit (not implicit/silent)

---

## Compliance

This governance process ensures:
- ✅ GDPR compliance (documented data handling)
- ✅ CCPA compliance (transparent privacy practices)
- ✅ COPPA compliance (no child data collection)
- ✅ HIPAA compliance (no health data)
- ✅ Transparency (all changes are public in git history)
- ✅ Accountability (all changes have reviewers)
- ✅ Immutability (git history is append-only)

---

## Questions?

- **Change approval process:** Open an issue tagged `[GOVERNANCE]`
- **Security concerns:** See [SECURITY.md](SECURITY.md)
- **Policy questions:** See [RETENTION_POLICY.md](RETENTION_POLICY.md)

---

**Last updated:** 2026-09-02  
**Policy version:** 1.0
