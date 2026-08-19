# Dyna Research Audit v1.0.1 Corrections

**Research cutoff preserved:** 2026-08-18  
**Correction date:** 2026-08-19  
**Decision:** **NOT READY** for `Dyna.html`

## Summary

This pass corrects factual wording, source classification, evidence semantics and claim prioritization without creating `Dyna.html`, changing `Sunday.html`, or reworking the portal. It modifies three registries and four existing Dyna documents, updates the main audit, and adds one marketing-language audit plus this correction report.

The registry now contains 33 sources, 68 claims and 11 demo/deployment records. The portal upgrade already exists as uncommitted changes in `README.md` and `index.html`; this pass validates but does not modify those files.

## Confirmed Corrections

1. **Scaling magnitude:** `1k → 10k → 100k → 1M` is four rungs spanning three orders of magnitude; the earlier magnitude label was incorrect.
2. **DYNA-1i was under-described:** the company publishes two quantitative 30-minute condition rows, not only qualitative videos.
3. **DYNA-1i seen condition:** 22 shirts in 30 minutes, reported as about 40 shirts/hour with average quality about grade 3+ [DYN-C061].
4. **DYNA-1i combined unseen condition:** 20 shirts in 30 minutes for a row labeled Lobby / Parking / CoRL, reported as about 40 shirts/hour with average quality about grade 3+ [DYN-C062].
5. **Bottle-cap duration:** the technical report says roughly 10 minutes of robot demonstrations; the corrected press release says 13 minutes of data. Both source-specific wordings are retained [DYN-C032, DYN-C068].
6. **Current marketing wording:** “zero custom engineering” is removed from the active homepage claim because it is absent from the current page inspected for the cutoff and no dated archive snapshot is registered.
7. **System attribution:** the current homepage separates DYNA-VLM, DYNA-2, DYNA-System0 and DYNA-SAUR. Integrated behavior should not be attributed entirely to Dyna-2 [DYN-C064–DYN-C067].
8. **Sunday lineage:** ALOHA, ACT, Mobile ALOHA, Diffusion Policy and UMI are founder research lineage; Sunday ACT-1/ACT-2 remain proprietary and their complete architectures are not public.
9. **Two-loop wording:** broad human-video pre-training plus deployment-fed robot post-training is retained only as a labeled `Research Inference`, not a Dyna term.
10. **Infrastructure attribution:** all Dyna-specific numbers now cite DYN-S007 and are described as internal workload-specific measurements. Joint pipeline gains are not attributed to Airflow, MCAP, H.264 or Alluxio alone when several changes occurred together.

## Schema Changes

### Source type

Twenty existing source records were reclassified into a more precise taxonomy. Important changes include:

- DYN-S003, DYN-S004 and DYN-S005: `Official Company Technical Post` because the pages' own category is `Company`;
- DYN-S006 and DYN-S007: retained as `Official Research`;
- DYN-S016: `Researcher Profile / Personal Primary Source`, not an official company source;
- DYN-S023: `External Dataset / Benchmark`;
- official tool and format references: `Documentation`;
- homepage, press releases and hosted customer case: `Official Company / Marketing`.

### External corroboration and independent verification

The ambiguous `third_party_verified` field was replaced by:

- `external_corroboration`: `None`, `External Dataset`, `Public Event`, `Named Customer`, `Independent Reporting`, `Investor Confirmation`, or `Other`;
- `independent_verification`: `Yes`, `No`, or `Partial`.

An external dataset, CoRL presentation, public video or Dyna-hosted customer testimonial does not count as independent verification. Only DYN-C059, the financing/valuation claim independently reported by Bloomberg, is currently `Yes`.

### Importance, risk and fix priority

The old overloaded priority field was replaced with:

- `claim_importance`: importance to understanding Dyna's technical route;
- `verification_risk`: risk from missing evidence, unclear scope or marketing abstraction;
- `fix_priority`: current research-maintenance urgency.

Current distribution:

| Dimension | Distribution |
|---|---|
| Claim importance | High 55 · Medium 7 · Low 6 |
| Verification risk | High 31 · Medium 23 · Low 14 |
| Fix priority | P0 18 · P1 38 · P2 12 |
| Independent verification | Yes 1 · Partial 1 · No 66 |

The claim registry also adds `evaluation_type`, `exact_wording`, `customer_site_status`, `attempt_denominator`, `runtime_window` and `robot_count` so commercial metrics retain their missing denominators instead of being flattened into a single evidence label.

## New Evidence

### DYNA-1i quantitative benchmark

The official company technical post defines a trial as a 30-minute continuous window measuring how many shirts are folded consecutively without failure [DYN-C063]. It publishes:

- Seen office: 22 shirts, about 40/hour, average quality about grade 3+.
- Combined unseen Lobby / Parking / CoRL: 20 shirts, about 40/hour, average quality about grade 3+.

This upgrades DYNA-1i from mostly qualitative evidence to **quantified internal company evidence**. It does not establish repeatability: one window per row is disclosed, with no independent repeats, confidence intervals, full failure logs, shirt distribution or per-unseen-environment breakdown.

### Current system architecture

The homepage currently assigns:

- DYNA-VLM: high-level agentic reasoning;
- DYNA-2: mid-level task dexterity;
- DYNA-System0: low-level whole-body control;
- DYNA-SAUR: embodiment and sensing through vision, tactile input and proprioception.

These are architecture labels, not complete module specifications. They create an attribution boundary for future reporting.

## Claims Downgraded

- DYN-S003/S004/S005-derived claims no longer inherit an `Official Research` source label; they now inherit `Official Company Technical Post`.
- CoRL demonstrations are `Public Event` corroboration with `independent_verification = No`.
- ABC usage is `External Dataset` corroboration, not independent scoring of Dyna's result.
- Monster Laundry is `Named Customer` corroboration, not an independent audit.
- Current 24/7, rapid-deployment and workflow-throughput language remains high verification risk because runtime, denominators, robot count and intervention data are absent.
- “Zero custom engineering” is removed from DYN-C052 rather than treated as current wording.

## Claims Upgraded

- DYN-C015 moves from weak qualitative framing to moderate evidence for a short internal seen-versus-combined-unseen comparison.
- DYN-C061 and DYN-C062 add the two quantitative condition rows with explicit windows, shirt counts, throughput and quality.
- DYN-C063 records the disclosed 30-minute trial definition. It is strong evidence for the company's metric definition, not for generalization beyond that protocol.

## Deleted / Historical Claims

`zero custom engineering` was deleted from the active current-homepage claim. A search-engine result indicates that the phrase appeared in a previously crawled homepage variant, but the registry has no dated official page or archive snapshot sufficient to classify it as a historical claim. It can be restored only with a stable dated source.

No other marketing claim was deleted. Learning in hours, deploying in minutes, scaling from one site to hundreds, production-grade performance, real-time recovery, 24/7 autonomy and the 100/180/200 workflow figures remain visible on the current homepage and remain explicitly company-reported.

## Remaining P0

The 18 remaining P0 claims are DYN-C005, C006, C010, C011, C017, C018, C019, C028, C031, C032, C038, C051, C052, C053, C054, C061, C062 and C068. They cluster into six unresolved questions:

1. complete logs and intervention accounting for DYNA-1;
2. protocols for zero-shot and low-data DYNA-1 claims;
3. independent audit of the 1M-hour corpus and cross-embodiment scaling;
4. statistical stability and exact data cost for the bottle-cap result;
5. customer-site denominators and commercial uptime;
6. independent repeats and complete logs for the new DYNA-1i rows.

## Ready for HTML?

**NOT READY.**

The information architecture is now strong enough to design a page, but publication should wait for manual verification of the 18 P0 records—especially the ten high-importance/high-risk claims highlighted in the handoff. Only one claim currently qualifies as independently verified. The official pages should also be archived so mutable homepage wording, dates and marketing changes can be audited later.

Readiness should change to `READY` when:

1. the ten priority claims receive source-line or archived-page verification;
2. the DYNA-1i combined unseen label and bottle-cap 10/13-minute difference are editorially signed off;
3. the portal and registries are committed as a stable baseline;
4. the future page implements module attribution and the external-corroboration/independent-verification distinction.
