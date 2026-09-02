# Data Retention and Deletion Policy

**MurMur retains zero user data. This page documents how and why.**

## Executive Summary

- MurMur does not collect, store, process, or retain any personal data
- No data deletion requests are possible (there is no data to delete)
- No data export requests are possible (there is no data to export)
- MurMur complies with GDPR Article 5 (data minimization) by design

## Detailed Retention Policy

### What MurMur Collects
**Nothing.** MurMur collects zero data:
- No personal data (names, emails, phone numbers, locations)
- No device identifiers (UUIDs, IDFA, AAID)
- No usage data (session logs, feature usage, analytics)
- No audio recordings (the microphone access is real-time only)
- No crash reports
- No feedback or survey data

### What MurMur Stores
**Nothing.** MurMur stores zero data:
- No local files (app-level databases, caches, logs)
- No cloud sync (iCloud, CloudKit, or third-party backends)
- No server infrastructure (no API, no database, no logging)

### Application-Level Behavior
**Theme preferences** are stored locally while the app is running:
- Theme selection is held in memory (RAM)
- All preferences are automatically cleared when the app is closed
- Preferences are never synced, logged, or analyzed
- No identifier links preferences to a user profile

### Server-Level Behavior
**Zero server infrastructure.** This repository is pure HTML documentation:
- No backend API
- No user accounts or authentication
- No database
- No server-side logging
- No analytics

GitHub Pages automatically logs HTTP requests (standard for web servers), but:
- MurMur does not collect or access these logs
- GitHub is not part of MurMur's privacy promise
- Logs contain only standard HTTP metadata (IP, timestamp, user-agent)
- See [GitHub's privacy policy](https://docs.github.com/en/site-policy/privacy-policies/github-privacy-statement) for details

### Third-Party Services
**Zero third-party integrations.** MurMur does not use:
- Analytics services (Google Analytics, Mixpanel, Segment, etc.)
- Crash reporting (Sentry, Crashlytics, Rollbar)
- Feedback platforms (Intercom, Zendesk, UserTesting)
- Ads networks (AdMob, AppLovin, Google Ads)
- SDKs or embedded libraries from third parties
- CDNs for app delivery (only App Store)

## Retention Timeline

| Data Type | Retention Duration |
|-----------|-------------------|
| User-provided data | Never collected |
| Device identifiers | Never collected |
| Usage analytics | Never collected |
| Crash reports | Never collected |
| Location data | Never collected |
| Health/fitness data | Never collected |
| Microphone audio | Nanoseconds (real-time analysis only) |
| Theme preferences | Until app is closed |

## Legal Compliance

### GDPR (General Data Protection Regulation)
- ✅ **Article 5** (Data Minimization): MurMur minimizes data by collecting zero data
- ✅ **Article 6** (Lawful Basis): No processing occurs, so lawful basis is not required
- ✅ **Article 25** (Privacy by Design): Zero-data architecture is the core design principle
- ✅ **Article 32** (Security): No data to secure
- ✅ **Article 35** (Impact Assessment): DPIA not required (zero processing of personal data)

### CCPA (California Consumer Privacy Act)
- ✅ **Right to Know**: No personal information exists to disclose
- ✅ **Right to Delete**: No personal information exists to delete
- ✅ **Right to Opt-Out**: No data collection to opt out from
- ✅ **Right to Non-Discrimination**: No discrimination possible (no data-driven decisions)

### COPPA (Children's Online Privacy Protection Act)
- ✅ **Parental Consent**: Not required (no data collection from anyone)
- ✅ **Data Security**: Not applicable (no data exists)
- ✅ **Data Retention**: Not applicable (no data retained)

### ePrivacy Directive (EU)
- ✅ No cookies set
- ✅ No unique identifiers used for tracking
- ✅ No personal data transmitted
- ✅ 100% offline (no network communication)

## Data Subject Requests

### Right to Access (GDPR Article 15)
**Response:** There is no personal data to access. MurMur does not maintain any records of personal information.

### Right to Erasure (GDPR Article 17)
**Response:** There is no personal data to erase. Closing the app automatically clears any in-memory preferences.

### Right to Data Portability (GDPR Article 20)
**Response:** There is no personal data to port. MurMur stores nothing that could be exported.

### Right to Object (GDPR Article 21)
**Response:** There is no processing to object to. MurMur does not process personal data.

## Change Log

Any changes to this policy are tracked in git history and automatically logged at: https://github.com/devbynorth/MurMurOfficial-Privacy/commits/main

**Version History:**
- 2026-09-02 — Initial publication
- (Future updates will be recorded here)

## Questions?

For questions about data retention or privacy, see:
- [Privacy Policy](privacy.html) (Norwegian)
- [Security Policy](SECURITY.md)
- [GitHub Issues](https://github.com/devbynorth/MurMurOfficial-Privacy/issues)

---

**Last updated:** 2026-09-02  
**Policy version:** 1.0
