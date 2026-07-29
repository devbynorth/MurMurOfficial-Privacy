# Sikkerhetsaudit – MurMurOfficial-Privacy

**Dato:** 2026-07-29  
**Revisor:** Claude (automatisert, read-only)  
**Commit:** `5e46ea0`

---

## Repoets omfang

| Dimensjon | Verdi |
|---|---|
| Runtime | Ingen – rent statisk HTML |
| Hoveddokument | `privacy.html` – norsk personvernerklæring for MurMur-appen |
| Rammeverk / backend | Ingen (ingen Node.js-app, ingen API-ruter, ingen database) |
| Deploy | GitHub Pages via `actions/deploy-pages` (trigger: push til `main`) |
| CI/CD | 7 GitHub Actions-workflows i `.github/workflows/` |
| Tredjepartsavhengigheter | Ingen `package.json` i repoet – verktøy installeres ad hoc i CI |
| Hemmeligheter i bruk | Kun `GITHUB_TOKEN` (automatisk provisjonert av GitHub) |
| Miljøvariabler | Ingen – ikke relevant for et statisk site |

Fordi repoet er et statisk informasjonsside uten server, database eller klientkode, er
kategoriene **klient/server-lekkasje**, **endepunkter**, og **database** ikke relevante og utelates.

---

## Funn sortert etter alvorlighet

### MIDDELS

---

#### M-1 — Alle Actions pinnet med tag, ikke commit-SHA

**Filer / linjer:**  
Alle 7 workflow-filer – hver linje med `uses:`:

```
actions/checkout@v4
actions/setup-node@v4
actions/configure-pages@v5
actions/upload-pages-artifact@v3
actions/deploy-pages@v4
actions/upload-artifact@v4
github/codeql-action/init@v3
github/codeql-action/analyze@v3
gitleaks/gitleaks-action@v2
```

**Hvorfor det er et problem:**  
Git-tagger er mutable. En kompromittert upstream-vedlikeholder (eller et overtatt npm-token) kan peke taggen til ny, ondsinnet kode uten at tagnavnet endres. CI-runneren kjører da ukjent kode med full tilgang til `GITHUB_TOKEN` og hele kjøremiljøet.

**Foreslått fiks:**  
Pin alle actions til den konkrete commit-SHA-en som tilsvarer taggen, og hold taggen som kommentar:

```yaml
uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4
```

Verktøy som `pin-github-action` eller Dependabot med `versioning-strategy: lockfile-only` kan automatisere dette.

---

#### M-2 — `workflow-lint.yml`: curl-pipe-installasjon uten sjekksum

**Fil:** `.github/workflows/workflow-lint.yml`, linje 21

```yaml
run: |
  bash <(curl -fsSL https://raw.githubusercontent.com/rhysd/actionlint/main/scripts/download-actionlint.bash)
```

**Hvorfor det er et problem:**  
Scriptet lastes ned og kjøres direkte i CI-runneren uten noen form for integritetssjekk. Hvis `rhysd/actionlint`-repoet blir kompromittert, eller trafikken manipuleres (selv om `-fsSL` bare følger redirects), kjøres vilkårlig kode med full tilgang til runner-miljøet og `GITHUB_TOKEN`.

**Foreslått fiks:**  
Enten (a) bruk den offisielle GitHub Action i stedet:

```yaml
uses: rhysd/actionlint@v1
```

eller (b) last ned binæren, verifiser SHA256-summen manuelt, og kjør deretter:

```yaml
- run: |
    curl -fsSL -o actionlint.tar.gz \
      https://github.com/rhysd/actionlint/releases/download/v1.7.7/actionlint_1.7.7_linux_amd64.tar.gz
    echo "FORVENTET_SHA256  actionlint.tar.gz" | sha256sum -c
    tar -xf actionlint.tar.gz actionlint
```

---

#### M-3 — Fem workflows og ett job mangler `permissions:`-blokk

**Filer:**
- `.github/workflows/ci.yml` (ingen `permissions:`)
- `.github/workflows/accessibility.yml` (ingen `permissions:`)
- `.github/workflows/dependency-audit.yml` (ingen `permissions:`)
- `.github/workflows/validate-package.yml` (ingen `permissions:`)
- `.github/workflows/workflow-lint.yml` (ingen `permissions:`)
- `dependency_scan`-jobben i `.github/workflows/security-monitoring.yml` (ingen `permissions:`)

**Hvorfor det er et problem:**  
Når `permissions:` utelates arver jobben repoets eller organisasjonens *default token permissions*. Hvis disse er satt til «permissive» (GitHub-standardinnstillingen for personlige repos), vil `GITHUB_TOKEN` ha `contents: write`, `pull-requests: write`, og lignende brede rettigheter. En kompromittert npm-pakke (`htmlhint`, `pa11y`, etc.) installert under jobben kan da bruke tokenet til å pushe kode eller manipulere PRer.

**Foreslått fiks:**  
Legg til eksplisitt minimumspermisjon på workflow- eller job-nivå. For rene lese/test-jobber er dette tilstrekkelig:

```yaml
permissions:
  contents: read
```

---

### LAV

---

#### L-1 — `id-token: write` eksponert for alle jobs i `deploy.yml`

**Fil:** `.github/workflows/deploy.yml`, linje 9–11

```yaml
permissions:
  contents: read
  pages: write
  id-token: write   # ← gjelder alle tre jobs
```

**Hvorfor det er et problem:**  
`id-token: write` er nødvendig kun for `deploy`-jobben (OIDC-token til `actions/deploy-pages`). Fordi tillatelsen er satt på workflow-nivå, arver også `validate`- og `build`-jobbene den. En kompromittert npm-pakke i `validate`- eller `build`-steget kan be om et OIDC-token og bruke det til å autentisere seg mot eksterne tjenester.

**Foreslått fiks:**  
Flytt `pages: write` og `id-token: write` ned til job-nivå på `deploy`-jobben, og begrens `validate` og `build` til `contents: read`:

```yaml
# Fjern top-level permissions-blokk, legg til per job:
  validate:
    permissions:
      contents: read
  build:
    permissions:
      contents: read
  deploy:
    permissions:
      contents: read
      pages: write
      id-token: write
```

---

#### L-2 — Unpinnede `npm install -g`-kall i fire workflows

**Filer / linjer:**
- `ci.yml`:25 – `npm install -g htmlhint`
- `deploy.yml`:31 – `npm install -g htmlhint`
- `dependency-audit.yml`:23 – `npm install -g npm-audit-html`
- `accessibility.yml`:26 – `npm install -g pa11y`
- `security-monitoring.yml`:50 – `pip install safety`

**Hvorfor det er et problem:**  
Uten versjonspin installeres alltid nyeste tilgjengelige versjon. Et kompromittert publiseringstoken til noen av disse pakkene (htmlhint, pa11y, npm-audit-html, safety) ville føre til at ondsinnet kode kjøres i CI-runneren ved neste bygge.

**Foreslått fiks:**  
Pin til spesifikke versjoner:

```yaml
run: npm install -g htmlhint@1.1.3
```

Vurder også å sette opp Dependabot for GitHub Actions-workflowene slik at pinner oppdateres automatisk via PR.

---

#### L-3 — `dependabot.yml` er konfigurert for `npm`, men repoet har ingen `package.json`

**Fil:** `.github/dependabot.yml`

```yaml
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "daily"
```

**Hvorfor det er et problem:**  
Dependabot finner ingen `package.json` og vil aldri opprette PRer. CI-verktøyene (`htmlhint`, `pa11y`, etc.) er dermed helt uten automatisert oppdateringsovervåking.

**Foreslått fiks:**  
Legg til overvåking av GitHub Actions-avhengigheter (og evt. fjern det ubrukelige npm-oppsettet):

```yaml
updates:
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```

---

#### L-4 — Ingen `.gitignore`

**Fil:** (mangler)

**Hvorfor det er et problem:**  
Repoet har ingen `.gitignore`. Lokalt genererte filer som `.env`, `*.key`, editor-tempfiler (`*.swp`, `.DS_Store`) eller verktøyoutput (`audit-report.html`) kan utilsiktet bli committet. Ingen av disse finnes i historikken per nå, men det er ingen teknisk sperring mot fremtidige ulykker.

**Foreslått fiks:**  
Legg til en minimal `.gitignore`:

```
.DS_Store
*.swp
.env
.env.*
node_modules/
audit-report.html
```

---

## Hemmeligheter (resultat)

| Sjekk | Resultat |
|---|---|
| `sk-*`, `sbp_*`, `whsec_*` i worktree | Ingen funn |
| `eyJ*` (JWT/base64) i worktree | Ingen funn |
| `-----BEGIN PRIVATE KEY` i worktree | Ingen funn |
| `ghp_`/`ghs_`/`gho_` GitHub-tokens | Ingen funn |
| `.env`-filer i historikken | Ingen funn |
| Hemmeligheter i slettede filer (`html-lint.yml`, `ci-cd.yml`, `codeql-analysis.yml`) | Ingen funn |
| Egendefinerte secrets brukt i workflows | Ingen (kun `GITHUB_TOKEN`) |

Ingen hemmeligheter funnet i worktree eller full git-historikk.

---

## Avhengigheter (resultat)

Ingen `package.json` eller `requirements.txt` i repoet. `npm audit` og `pip-audit` er ikke relevante. Verktøy brukt i CI (`htmlhint`, `pa11y`, `npm-audit-html`, `safety`) er ikke deklarerte avhengigheter — se L-2 og L-3.

---

## Klient/server-lekkasje, endepunkter, database

Ikke relevant. Repoet er en ren statisk HTML-side uten JavaScript-runtime, API-ruter, miljøvariabler eller database.

---

## CSP-merknad (informasjon)

`privacy.html` linje 7:

```html
<meta http-equiv="Content-Security-Policy"
      content="default-src 'none'; style-src 'unsafe-inline'; form-action 'none'; base-uri 'none';">
```

`style-src 'unsafe-inline'` er nødvendig fordi siden bruker en `<style>`-blokk. Siden det ikke finnes brukerinput, scripts, eller dynamisk innhold, er den praktiske risikoen lav. En hash-basert policy (`'sha256-...'`) ville eliminert behovet for `unsafe-inline`, men er ikke nødvendig her.

---

## Oppsummering

| Alvorlighet | Antall |
|---|---|
| Kritisk | 0 |
| Høy | 0 |
| Middels | 3 |
| Lav | 4 |

Repoet har en **svært begrenset angrepsflate** — én statisk HTML-fil, ingen runtime, ingen brukerdata. Alle funn er knyttet til CI/CD-pipeline og supply chain. Ingen hemmeligheter er eksponert.

Prioritert rekkefølge for utbedring:
1. **M-3** – Legg til `permissions: contents: read` på alle workflows uten eksplisitte tillatelser (liten endring, stor effekt)
2. **M-1** – Pin actions til SHA (kan automatiseres med Dependabot `github-actions`)
3. **M-2** – Bytt curl-pipe-installer i `workflow-lint.yml` med offisiell action eller verifisert nedlasting
4. **L-1** – Flytt `id-token: write` ned til `deploy`-jobben
5. **L-2** – Pin npm-pakkeversjonene
6. **L-3** – Konfigurer Dependabot for `github-actions`
7. **L-4** – Legg til `.gitignore`
