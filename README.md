# MurMur — Public Signal Site & Privacy 🔒

This repository hosts the public MurMur Signal homepage and privacy pages via GitHub Pages.

## Published routes

- `index.html` — public PapiiDLéon ◈│◈ MurMur homepage
- `signal/privacy/index.html` — privacy information for MurMur Signal contact and lead-form submissions
- `privacy.html` — existing privacy policy for the MurMur iOS app

The iOS app privacy policy remains separate because that app is designed around offline use and no personal-data collection, while the Signal lead form may collect name, email, project type and project information voluntarily submitted by a visitor.

## Deployment

The GitHub Pages workflow validates all HTML, then publishes the homepage, the Signal privacy route and the unchanged iOS app privacy route on pushes to `main`.

## Safety principles

- No credentials, API keys or tokens in frontend code.
- No analytics or tracking by default.
- No form may claim successful submission unless a backend is actually configured.
- Privacy statements must match the actual data flow.
- Consequential production changes should be reviewed before merge.

## Contact

MurMurOfficial@icloud.com

Built and maintained by **PapiiDLéon ◈│◈ MurMur**.
