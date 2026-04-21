# COCOMO II Valuation of OpenMRS

*Post-Architecture model, measured from the github.com/openmrs organization on 21 April 2026.*

## Headline

| Metric | Value |
|---|---|
| Adjusted size | **~757 KSLOC** |
| Effort | **~4,913 person-months (~409 person-years)** |
| Nominal schedule | **~54 months (~4.5 years)** |
| Average parallel staff | **~91 FTE** |
| Estimated value @ $15,000/PM | **~$73.7 million** |

## What I included

The "full OpenMRS ecosystem" was taken to be 24 canonical repositories from `github.com/openmrs`, grouped as:

- **Backend platform** — `openmrs-core`
- **Representative backend modules** — `openmrs-module-fhir2`, `openmrs-module-htmlformentry`, `openmrs-module-referenceapplication` (sampled; OpenMRS has dozens more modules, so this undercounts the module ecosystem)
- **O3 microfrontend monorepos** — `openmrs-esm-core`, `openmrs-esm-home`, `openmrs-esm-patient-chart`, `openmrs-esm-patient-management`, `openmrs-esm-laboratory-app`, `openmrs-esm-form-builder`, `openmrs-esm-fast-data-entry-app`, `openmrs-esm-dispensing-app`, `openmrs-esm-billing-app`, `openmrs-esm-stock-management`, `openmrs-esm-admin-tools`, `openmrs-esm-cohortbuilder-app`, `openmrs-esm-module-management-app`, `openmrs-esm-template-app`, `openmrs-esm-user-onboarding`
- **Form engines** — `openmrs-ngx-formentry` (Angular), `openmrs-form-engine-lib` (React)
- **Distribution & content** — `openmrs-distro-referenceapplication`, `openmrs-content-referenceapplication`
- **Mobile** — `openmrs-contrib-android-client`

Repos were shallow-cloned (`--depth 1`) and measured with `cloc` 2.09, excluding `node_modules`, `dist`, `build`, `target`, `.yarn`, `.pnp`, `coverage`, and similar directories.

## From cloc output to COCOMO SLOC

`cloc` reports total non-blank, non-comment lines by language. COCOMO expects logical source lines of code. I weighted each language according to its likely contribution to development effort:

- **Full weight (1.0)**: Java, Kotlin, TypeScript, JavaScript, Groovy, Velocity — these are where the real engineering happens.
- **Partial weight**: XML 0.25, HTML 0.5, SCSS/CSS 0.4, SQL 0.5, Maven 0.25, Gradle 0.25, Properties 0.2 — real effort but less dense than procedural code. Hibernate/Spring/Liquibase XML in OpenMRS core alone is 169K lines, hence the 0.25 rather than full-credit treatment.
- **Zero weight**: JSON, Markdown, YAML, SVG, shell scripts, Dockerfiles, Jupyter, CSV data, plain text — configuration/data/docs, not engineering effort.

### Per-repo breakdown

| Repo | cloc total | Full-weight code | Partial (weighted) | Adjusted SLOC |
|---|---:|---:|---:|---:|
| openmrs-core | 375,495 | 155,618 | 53,676 | **209,294** |
| openmrs-module-fhir2 | 117,887 | 104,261 | 2,551 | 106,812 |
| openmrs-esm-patient-chart | 123,841 | 92,408 | 3,493 | 95,901 |
| openmrs-esm-patient-management | 79,548 | 54,640 | 2,445 | 57,085 |
| openmrs-esm-core | 77,003 | 48,300 | 1,784 | 50,084 |
| openmrs-module-htmlformentry | 54,874 | 44,915 | 1,933 | 46,848 |
| openmrs-esm-billing-app | 36,587 | 30,905 | 570 | 31,475 |
| openmrs-ngx-formentry | 72,857 | 28,751 | 2,021 | 30,772 |
| openmrs-contrib-android-client | 39,187 | 25,428 | 2,079 | 27,507 |
| openmrs-esm-stock-management | 29,581 | 21,093 | 826 | 21,919 |
| openmrs-form-engine-lib | 96,128 | 19,087 | 291 | 19,378 |
| openmrs-esm-dispensing-app | 17,087 | 13,130 | 340 | 13,470 |
| openmrs-esm-form-builder | 18,507 | 12,061 | 577 | 12,638 |
| openmrs-esm-admin-tools | 9,632 | 6,457 | 276 | 6,733 |
| openmrs-esm-cohortbuilder-app | 7,451 | 6,247 | 133 | 6,380 |
| openmrs-esm-fast-data-entry-app | 8,799 | 6,120 | 253 | 6,373 |
| openmrs-module-referenceapplication | 21,057 | 1,724 | 2,680 | 4,404 |
| openmrs-esm-laboratory-app | 6,172 | 3,604 | 149 | 3,753 |
| openmrs-esm-user-onboarding | 3,199 | 2,316 | 41 | 2,357 |
| openmrs-esm-module-management-app | 1,866 | 1,500 | 68 | 1,568 |
| openmrs-esm-template-app | 2,011 | 1,190 | 50 | 1,240 |
| openmrs-esm-home | 1,554 | 966 | 20 | 986 |
| openmrs-distro-referenceapplication | 5,263 | 0 | 176 | 176 |
| openmrs-content-referenceapplication | 367 | 0 | 57 | 57 |
| **Total** | | **680,721** | **76,592** | **757,313** |

## COCOMO II Post-Architecture formula

$$PM = A \cdot (Size)^E \cdot \prod_{i=1}^{17} EM_i$$

where

$$E = B + 0.01 \cdot \sum_{j=1}^{5} SF_j$$

and schedule

$$TDEV = C \cdot (PM)^{D + 0.2 \cdot 0.01 \cdot \sum SF_j}$$

Calibrated constants (Boehm 2000): A=2.94, B=0.91, C=3.67, D=0.28.

## Scale factor ratings

| Factor | Rating | Value | Rationale |
|---|---|---:|---|
| **PREC** — Precedentedness | High | 2.48 | EMR domain is well-precedented; OpenMRS itself has been iterating on this problem since 2004. |
| **FLEX** — Development flexibility | High | 2.03 | OSS project with considerable flexibility in requirements; implementers tailor to their sites. |
| **RESL** — Architecture/risk resolution | Nominal | 4.24 | Mature overall, but the ongoing O3 frontend migration shows architecture risk is actively being resolved, not fully settled. |
| **TEAM** — Team cohesion | Nominal | 3.29 | Globally distributed community with basically cooperative interactions; inevitable coordination friction across implementer orgs. |
| **PMAT** — Process maturity | Low | 6.24 | CI, PR reviews, release cadence — roughly CMM level 2 equivalent; no formal process assessment. |

Sum of SF = **18.28**, giving **E = 0.91 + 0.1828 = 1.0928** (slight diseconomy of scale).

## Effort multipliers

| Multiplier | Rating | Value | Rationale |
|---|---|---:|---|
| RELY (required reliability) | High | 1.10 | Medical records; errors affect patient care. |
| DATA (database size) | High | 1.14 | Concept dictionary, patient data schema, FHIR resources. |
| CPLX (product complexity) | High | 1.17 | Complex data model, form engines, FHIR, workflow, multi-module plugin architecture. |
| RUSE (reusability) | High | 1.07 | Explicitly designed for reuse: modules, microfrontends, shared framework. |
| DOCU | Nominal | 1.00 | Wiki + per-repo READMEs roughly match needs. |
| TIME | Nominal | 1.00 | No tight execution-time constraint. |
| STOR | Nominal | 1.00 | No main-storage constraint. |
| PVOL (platform volatility) | High | 1.15 | Spring upgrades, React ecosystem churn, O3 migration. |
| ACAP (analyst capability) | Nominal | 1.00 | Mixed community, core analysts strong. |
| PCAP (programmer capability) | Nominal | 1.00 | Mixed community. |
| PCON (personnel continuity) | Low | 1.12 | OSS has high contributor churn. |
| APEX (applications experience) | High | 0.88 | Contributors understand EMR/OpenMRS domain. |
| PLEX (platform experience) | High | 0.88 | Java/Spring/React/TS are well-known. |
| LTEX (language/tool experience) | High | 0.91 | Commonly used languages. |
| TOOL (software tools) | High | 0.90 | Modern CI, linters, test infra. |
| SITE (multisite development) | High | 0.93 | Globally distributed but strong electronic communication (GitHub, Slack, Talk forum). |
| SCED (schedule constraint) | Nominal | 1.00 | OSS ships when ready; no schedule compression. |

Product of EMs = **1.1927**.

## Computed results

$$PM = 2.94 \cdot (757.3)^{1.0928} \cdot 1.1927 \approx 4{,}913 \text{ person-months}$$

$$TDEV = 3.67 \cdot (4913)^{0.28 + 0.003656} \approx 54 \text{ months}$$

| Output | Value |
|---|---|
| Effort | 4,913 PM ≈ 409 person-years |
| Nominal schedule | 54 months ≈ 4.5 years |
| Average headcount | ≈ 91 FTE over the schedule |
| **Cost @ $15,000/PM (US fully-burdened)** | **~$73.7 million** |

## Sanity checks

- **Per-LOC cost**: $73.7M / 757K ≈ **$97/LOC**. Basic COCOMO ("Organic" mode at US rates) typically lands around $30–40/LOC. The ~3× multiplier comes from the combined effect of E > 1 (diseconomies of scale on 757 KSLOC) and the above-nominal effort multipliers for reliability, complexity, and platform volatility. For a complex, distributed medical system this feels defensible.
- **Against calendar time**: OpenMRS has been developed for ~22 years. 409 person-years over 22 years ≈ 18 FTE/year average across the whole community — plausible for a project that has a small paid core plus many implementer and volunteer contributors. COCOMO's 91-FTE figure is the nominal *parallel* staffing to hit the 54-month schedule; OSS stretches schedule in exchange for lower parallel staffing, but total effort should be comparable.
- **Against peer OSS**: Open Hub has historically estimated openmrs-core alone around the low-to-mid hundreds of thousands of LOC with valuations in the $5-10M range using basic COCOMO conventions. Our ~$20M valuation *for core alone* (209K adjusted SLOC × implied multiplier) is higher, reflecting (a) the stricter COCOMO II post-architecture model and (b) above-nominal complexity/reliability ratings appropriate to a medical system.

## Caveats

- **Module ecosystem is undercounted.** OpenMRS has dozens of community backend modules (appointment scheduling, CIEL dictionary tools, reporting, OCL, etc.) that were not included. Adding them would likely increase the total by 20–50%.
- **SLOC weighting is judgment-based.** Different defensible weightings of XML/SCSS/HTML could move the headline figure by ±15%.
- **Effort multipliers are opinions.** Walking through the 17 EMs with someone who actually works on OpenMRS could shift the EM product materially; most likely direction is *up* (higher CPLX, higher RELY, worse PCON than I rated).
- **COCOMO II is calibrated to traditional, commercially-managed projects.** Applying it to an OSS community project is a category stretch — we are essentially asking "what would it have cost a single organization to build this?" which is a useful thought experiment but not the same thing as OpenMRS's actual resourcing.
- **Labor rate assumption.** The $15K/PM figure is a US fully-burdened convention. At a global blended rate closer to $7–8K/PM, the headline drops to roughly **$36M**.

## Files

- Per-repo cloc JSON outputs: `openmrs/cloc-results/`
- Aggregated SLOC data: `openmrs/aggregate.json`
- Full computed result: `openmrs/cocomo_result.json`
