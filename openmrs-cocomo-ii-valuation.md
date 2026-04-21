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

## Confidence review — strengths and weaknesses

### Overall confidence

If someone asked "is OpenMRS worth roughly $50M–$150M as a software asset under US labor conventions?" the answer is yes with reasonable confidence. If they asked "is it $73.7M specifically?" the answer is no — that precision is false. The headline is really the midpoint of a wide band, and the band is probably wider than ±30%.

### Strengths

**The size measurement is the strongest part.** The repos were actually cloned and measured with `cloc` at a specific point in time, with a defensible language-weighting scheme. The 757 KSLOC figure is reproducible — someone else running the same excludes against the same commits would get essentially the same number. The inputs are visible, not guessed.

**The formula application is mechanical and correct.** The COCOMO II math — scale factors, effort exponent, multiplier product, schedule equation — is textbook Boehm 2000 calibration, applied correctly. If you accept the inputs, the outputs follow.

**One real data-quality issue was caught.** The initial cloc of `openmrs-ngx-formentry` was inflated by a vendored 147K-line `yarn.js` file in `.yarn/releases/`. It was flagged as suspicious (~734 LOC per file is not TypeScript) and re-run with stronger excludes. That kind of check is often skipped in automated valuations, and when it's skipped the number is nonsense.

**The sanity checks land in the right neighborhood.** $97/LOC for a complex medical system with COCOMO II's post-architecture complexity penalties is consistent with published estimates for similar-complexity systems. The per-repo pattern (core platform dominating, O3 monorepos second tier, small satellite apps) matches what you'd expect if you know the project.

### Weaknesses — in order of how much they matter

**1. Most of the backend modules are not included.** This is the largest weakness. OpenMRS's module ecosystem is enormous — appointment scheduling, CIEL, reporting, OCL client, reference metadata, Bahmni integration, reporting compatibility, metadata sharing, and dozens more, some very substantial. Only three modules (fhir2, htmlformentry, referenceapplication) were sampled as "representative." A serious valuation would enumerate the `openmrs-module-*` prefix across the org and include everything actively maintained. A reasonable guess is that adding them would bring total SLOC to 1M–1.3M and push the valuation toward $110M–$140M. So the $73.7M headline is almost certainly *low*.

**2. The language weighting is judgment, not science.** XML was weighted 0.25, SCSS 0.4, HTML 0.5, JSON 0 — those numbers aren't calibrated against anything. Boehm himself is agnostic; the COCOMO II manual tells you to count "logical SLOC of the delivered product" and leaves language equivalence to the estimator. If XML were given full weight, openmrs-core alone would jump by ~130K SLOC and the total would climb 15–20%. If all non-Java/TS/Kotlin were excluded entirely, it would drop by ~10%. This single decision swings the headline more than any other input.

**3. The effort multipliers are opinions about OpenMRS, not measurements.** The seventeen EMs were rated from public knowledge of the project. With an actual OpenMRS committer walking through them, several would likely move — CPLX might go from High to Very High (form engines + FHIR + concept dictionary really is unusually complex), PCON might go worse than Low (serious contributor churn in OSS medical software), RELY might go Very High (this is literally a medical record). Each notch up on an EM multiplies the result. If three EMs went up one level each, the headline could climb 40%.

**4. The scale factors have the same issue but matter more.** Scale factors are *exponents* on size, so small rating errors compound. PMAT was called "Low" (CMM level 2 equivalent) — defensible for OSS with CI and reviews but no formal process. If someone argued PMAT should be "Very Low" (no defined process), sum of SF would rise by ~1.5 and E would jump from 1.0928 to ~1.108, which on 757 KSLOC adds about 500 person-months (~$7M). RESL is similar — rated Nominal because of the O3 migration; an argument for Low is easy to make.

**5. COCOMO II is the wrong shape for OSS.** This is the philosophical weakness. The model assumes a single organization allocating staff on a schedule to hit a delivery date. OpenMRS is a 22-year rolling community project with a small paid core and many intermittent contributors from implementer organizations. The 91-FTE / 54-month picture is a counterfactual ("if a single firm built this from scratch on a schedule") — useful as a cost floor for a reimplementation, but *not* OpenMRS's actual investment profile. The real question is often what you're trying to answer. If it's "how much would it cost a company to recreate this" — reasonable. If it's "what has the community spent on it over 22 years" — actual historical PY is probably much higher than 409, because rewriting old code happened many times and the calendar was non-linear.

**6. COCOMO II's 2000 calibration is old.** Boehm's coefficients (A=2.94, B=0.91) were fit to 161 projects completed in the 1990s. Software economics have changed — more reuse, better tools, more open-source leverage, but also more complex integration surfaces. Academic work since (COCOMO III drafts, COSYSMO, function-point-based alternatives) exists but hasn't replaced it. The model is a well-worn instrument, not a calibrated one.

**7. The $15K/PM rate is a convention, not a measurement.** It's the standard Boehm-era fully-burdened US rate, still widely cited, but actual 2026 fully-burdened developer cost in the US is more like $20–25K/PM depending on location and level. At $22K/PM the headline would be $108M. At the global blended $7.5K/PM (more representative of who actually contributes to OpenMRS), it would be $37M. The ~3× spread from rate alone is often larger than the spread from any other input.

### How to tighten this

If a defensible number (rather than a decent first pass) is needed:

- **Enumerate the full `openmrs-module-*` catalog** from the GitHub org and include the top 20–30 by commit activity — that's the biggest omission.
- **Get an OpenMRS committer** (or at minimum someone who's been around long enough to rate PMAT, CPLX, PCON, RELY with authority) to walk through the 22 COCOMO II inputs. Each rating takes 30 seconds; the total conversation is under 15 minutes and would materially tighten the estimate.
- **Decide explicitly what question the valuation is answering** — "reimplementation cost" vs. "historical investment" vs. "community asset value" — because the three give different numbers and use different labor rates.
- **Present the result as a range** with a Monte Carlo over the inputs that are least certain (the language weights and the mid-ranked EMs), rather than a single point estimate.

### Bottom line

What is presented here is a credible back-of-envelope that gets to the right order of magnitude and identifies where the sensitivity lives. It is not a number to put in a grant proposal, a valuation report for a donor, or an M&A discussion without the tightening above.
