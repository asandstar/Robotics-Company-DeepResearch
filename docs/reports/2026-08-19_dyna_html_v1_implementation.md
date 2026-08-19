# Dyna.html v1 Implementation Report

**Report date:** 2026-08-19  
**Research cutoff:** 2026-08-18  
**Artifact:** `Dyna.html`  
**Status:** READY FOR HUMAN REVIEW

## Delivered artifact

- 36 semantic `<section>` elements, including the four chapter containers, cover and publication boundary.
- 69 public claims embedded from `data/dyna_claim_registry_v1.csv`.
- 33 structured sources embedded from `data/dyna_source_registry_v1.csv`.
- 11 demo/deployment records embedded from `data/dyna_demo_deployment_audit_v1.csv`.
- Single-file HTML/CSS/vanilla-JavaScript implementation with no runtime `fetch()`, framework, package manager or build dependency.
- The CSV registries remain the source of truth; embedded JSON is a publication snapshot for `file://` compatibility.

## Main interactions

- Sticky table of contents and reading-progress indicator.
- Mobile navigation drawer.
- Dark/light mode with local preference persistence.
- Expandable research timeline, infrastructure pipeline, deployment records, claims and research questions.
- Three-stage evidence explorer.
- Claim Explorer with full-text search and filters for importance, verification risk, fix priority, source type, external corroboration, independent verification and topic.
- High-importance/high-verification-risk view generated from the embedded registry rather than a manually maintained list; current count: 28.
- Deployment ledger search and evidence filters.
- Glossary and source search.
- Direct source links on relevant sections and evidence cards, print/PDF mode and back-to-top control.
- Visible focus states, semantic controls, reduced-motion support and responsive table containers.

## Post-publication visual refinement

- Consolidated the accumulated CSS into one structural foundation, one editorial theme layer and one responsive rule set; duplicate mobile breakpoints and obsolete chapter overrides were removed.
- Restored explicit grid foundations for two-, three- and four-column cards, filters, detail fields, the scaling ladder, score chart and source list.
- Added source-bounded research briefs to Data Engine, Three Evidence Stages, WAM and Infrastructure. These briefs distinguish dataset source from supervision, offline prediction from physical execution, training objective from inference behavior, and storage scale from end-to-end systems bottlenecks.
- Added direct official-source links inside the DYNA-VLM / DYNA-2 / DYNA-System0 / DYNA-SAUR architecture rows. Only DYNA-2 is linked as a standalone technical report; the other modules retain an explicit current-stack or related-demo boundary.
- Centered metrics, stage selectors and process diagrams selectively while preserving left-aligned long-form reading text.
- Increased desktop whitespace and normalized mobile padding. Light-theme body, muted and accent text pass WCAG AA contrast checks; visible form boundaries reach 3:1 against white.

## Evidence safeguards

1. The 20 → 28 → 45 → 53 figure is labeled as a mean normalized score over 14 heterogeneous physical tasks, not a universal success rate.
2. The 39-task stage is labeled offline robot action prediction, not physical zero-shot execution.
3. The 14-task physical stage explicitly requires task-specific robot post-training and reports the disclosed trial counts.
4. Future-video prediction is separated as a training objective from inference-time imagined-video planning; the disclosed scaling policy is described as action prediction without required future-video generation.
5. External corroboration and independent verification remain separate fields throughout the report.
6. Company-run evaluation, public events, external datasets and hosted customer testimonials are not promoted to independent replication.
7. Infrastructure figures are labeled `Internal engineering measurement`, `Workload-specific` and `DYN-S007`; joint redesign gains are not assigned to one tool.
8. Integrated-stack behavior is not automatically attributed to Dyna-2.
9. Empty or not-applicable registry values render as `Unknown / Not disclosed` in the Claim Explorer.
10. Sunday–Dyna comparison is conceptual and contains no cross-company success-rate comparison.

## Pre-build editorial fixes

### DYNA-1i unseen row

The registry and timeline already used the corrected interpretation: a single reported 30-minute unseen-condition row labeled `Lobby / Parking / CoRL`. The page preserves that wording and states that the per-environment breakdown, aggregation method and independent repeats are unknown. It does not describe three separate 30-minute tests.

### 14-task / 15-task discrepancy

Added `DYN-C069` to `data/dyna_claim_registry_v1.csv` and a new phrase-level row plus editorial rule to `docs/dyna/dyna_marketing_vs_technical_claims_v1.md`. The page treats the earlier press-release wording (`15 benchmark tasks`, DYN-S033) and the later technical report (`14-task physical suite`, DYN-S006) as a source-version discrepancy. Scientific analysis uses the later, enumerated 14-task suite and does not speculate about the cause.

## Other editorial boundaries

- The bottle-cap wording remains source-specific: the technical report says roughly 10 minutes of robot demonstrations; the corrected press release says 13 minutes of data. The page does not collapse these into one precision level or equate recording time with total engineering effort.
- The system diagram uses the current public attribution: DYNA-VLM for high-level reasoning, DYNA-2 for mid-level dexterity, DYNA-System0 for low-level control and DYNA-SAUR for embodiment/sensing. It is labeled as a high-level module positioning rather than a complete dependency graph.
- Unknown information remains explicit. No missing corpus, safety, reliability, deployment or economics field is filled by inference.

## Automated validation

| Check | Result |
|---|---|
| HTML parser acceptance | PASS |
| Duplicate IDs | PASS — none |
| Internal anchors resolve | PASS |
| `index.html` → `Dyna.html` | PASS |
| `index.html` → `Sunday.html` | PASS |
| Embedded claim count equals registry | PASS — 69 = 69 |
| Unique `claim_id` | PASS |
| Unique `source_id` | PASS |
| Claim `source_id` foreign keys | PASS |
| Claim filter controls present | PASS |
| Responsive/mobile CSS and overflow containers | PASS (static) |
| Print CSS | PASS |
| Static external-link safety attributes | PASS |
| Dynamically generated source-link safety attributes | PASS (code inspection) |
| JavaScript syntax (`node --check`) | PASS |
| Misleading `53% success` wording | PASS — absent |
| 39-task physical-zero-shot wording | PASS — absent |
| Future-video inference boundary | PASS |
| Chinese-source domains | PASS — none |
| `Sunday.html` SHA-256 | PASS — `4c1e6a64c3f00297ef0167acba7a9fdcf26e2889f2c17399e3d4217bf8e88cd2` unchanged |
| Git diff contains `Sunday.html` | PASS — no |
| Unrelated mass formatting | PASS — none observed |

## Remaining human-review items

The browser-control environment exposed no browser instance, so real click, screenshot and viewport testing could not be completed in this pass. Static responsive rules, JavaScript syntax and interaction wiring passed inspection, but the following remain the highest-value manual checks:

1. Cover hierarchy and the thesis flow at desktop and 390 px mobile widths.
2. Sticky table-of-contents scroll highlighting and mobile drawer focus behavior.
3. DYNA-1i expanded timeline wording for the single combined unseen row.
4. Data Engine inline-SVG legibility and arrow semantics.
5. Three Evidence Stages keyboard operation and content replacement.
6. 20 → 28 → 45 → 53 chart labeling in screen and print/PDF output.
7. WAM training-versus-inference and `World Model ≠ Imagined-Video Planning` callout.
8. Infrastructure accordion density and the workload-specific labels on every metric.
9. Claim Explorer combined filters, empty-value rendering and long-field wrapping.
10. Source URLs and page-break behavior in an exported PDF.

The report is published from `main` through the repository's GitHub Pages workflow; releases remain subject to human visual review.
