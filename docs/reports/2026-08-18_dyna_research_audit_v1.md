# Dyna Robotics Research Audit v1

**Research cutoff:** 2026-08-18  
**Audience:** robot-learning researchers and technical diligence readers  
**Evidence basis:** 33 English-language sources registered in [`data/dyna_source_registry_v1.csv`](../../data/dyna_source_registry_v1.csv). Claim IDs below resolve to [`data/dyna_claim_registry_v1.csv`](../../data/dyna_claim_registry_v1.csv).

## Audit Corrections v1.0.1

This correction pass preserves the 2026-08-18 research cutoff and changes the audit in eight material ways:

1. **Scaling ladder:** corrected the earlier magnitude label to a **four-rung ladder spanning three orders of magnitude**: 1k, 10k, 100k and 1M hours.
2. **DYNA-1i evidence:** added the disclosed 30-minute quantitative seen-office result (22 shirts, about 40/hour, average quality about grade 3+) and combined unseen Lobby / Parking / CoRL result (20 shirts, about 40/hour, average quality about grade 3+). Each is one company-reported window; repeats, confidence intervals and full logs are not public [DYN-C061–DYN-C063].
3. **Source taxonomy:** DYNA-1, DYNA-1i and DYNA-1 Pre-Training are now `Official Company Technical Post`, matching their on-page `Category: Company`; Dyna-2 and its infrastructure report remain `Official Research`. Jason Ma's page is a `Researcher Profile / Personal Primary Source`.
4. **Verification schema:** external dataset provenance, a public event and a Dyna-hosted customer testimonial are recorded as `external_corroboration`, not independent verification. `independent_verification` is reserved for a third party that actually verifies the specific Dyna claim.
5. **Marketing history:** “zero custom engineering” was removed from the active homepage claim because the current page inspected for the cutoff does not show it and no dated archive snapshot is registered. Current learn-in-hours, deploy-in-minutes and one-site-to-hundreds wording remains an unverified company claim.
6. **System architecture:** added the homepage's layered attribution: DYNA-VLM for high-level agentic reasoning, DYNA-2 for mid-level task dexterity, DYNA-System0 for low-level whole-body control and DYNA-SAUR for embodiment/sensing [DYN-C064–DYN-C067].
7. **Sunday comparison:** the comparison now identifies ALOHA, ACT, Mobile ALOHA, Diffusion Policy and UMI as founder research lineage, while treating Sunday ACT-1/ACT-2 as proprietary architectures whose complete designs are not public.
8. **Remaining high-importance/high-risk claims:** the million-hour corpus, cross-embodiment scaling, small-data bottle-cap result, customer-site 87%/46%, 24/7 autonomy, rapid deployment, customer acceptance metrics and DYNA-1i repeatability still need stronger evidence.

## 1. One-page technical summary

Dyna's public technical thesis has two layers. The research layer argues that robot learning can borrow scale from human egocentric video: use estimated wrist motion and grasp aperture as pseudo-actions, jointly learn future action and future video, then post-train the resulting representation on task-specific robot demonstrations. The commercial layer argues that narrow production deployments create the feedback, recovery and infrastructure loop needed to turn this pre-training into useful systems.

The route evolved materially. DYNA-1 emphasized long-horizon recovery in a narrow napkin-folding system, supported by a foundation reward model and continuous deployment data [DYN-S003]. DYNA-1i targeted environment shift with tens of hours of office-collected robot data and disclosed two short quantitative condition rows rather than only qualitative demonstrations [DYN-S004]. A later DYNA-1 update claimed selected zero-shot behaviors and one-hour adaptation, but disclosed too little protocol to support broad comparison [DYN-S005]. DYNA-2 then made human-video hours the controlled scaling variable and introduced a four-rung 1k/10k/100k/1M-hour ladder spanning three orders of magnitude, a 39-task offline robot suite and a 14-task physical post-training suite [DYN-S006]. The infrastructure report explains how Dyna made experiments at that scale operationally repeatable [DYN-S007].

The strongest scientific result is narrower than the headline. Dyna reports monotonic improvement in held-out human action prediction, zero-shot offline robot action prediction, and a selected 14-task on-robot suite as human pre-training hours increase. Its ablations also report that future-video prediction improves action prediction and that video without pseudo-action labels contributes at larger scale. These are structured internal experiments, but not a released dataset, model, codebase, peer-reviewed paper or independent replication.

“World-Action Model” must be interpreted carefully. In the disclosed scaling-law variant, future-video prediction is a **training objective**. At policy inference, the action stream does not generate or attend to imagined future video. Dyna-2 therefore does not demonstrate online planning by rolling out video futures; the company argues that world prediction improves the shared representation used by a reactive action policy [DYN-C021].

The scaling law is likewise specific: its primary independent variable is hours of human video under one model family. Model size and compute scaling are explicitly deferred. The physical evaluations still use task-specific robot post-training—up to ten hours per task. The bottle-cap example is described as roughly ten minutes in the technical report and 13 minutes in the corrected company press release [DYN-C032, DYN-C068]. There is no disclosed human–robot alignment or co-training stage in that suite. Offline cross-embodiment prediction is promising evidence, but it is not equivalent to zero-shot physical task success.

Dyna's infrastructure contribution is unusually concrete for a company report: MCAP/H.264 storage, topic-group chunking, Airflow ingestion, warehouse-backed manifests, memory mapping, cluster-local NVMe caching, topology-aware optimizer sharding, preflight gating and checkpoint recovery. Reported before/after numbers are useful engineering hypotheses, but remain internal measurements with hardware, cost and workload details partly undisclosed.

Commercial evidence is much weaker than the scientific protocol. Current homepage claims such as 24/7 autonomy, learning in hours, deploying in minutes, scaling from one site to hundreds and task throughput figures have no public audit protocol [DYN-S001]. A named Monster Laundry case externally corroborates real customer use, but is hosted by Dyna, is not independently verified, and still involves attendants loading and retrieving batches [DYN-S013]. Most importantly, Dyna's own May 2026 retrospective says early deployments required weeks to months of on-site engineering and that deployment-to-deployment learning had not yet compounded reliably [DYN-S012]. This candid disclosure is central to interpreting the marketing language.

The current evidence supports this conclusion: Dyna has presented a coherent human-video scaling program, a substantial internal Dyna-2 evaluation and a credible million-hour systems account. It has not yet publicly established an independently verified general scaling law, zero-shot physical cross-embodiment control, fleet-wide 24/7 autonomy, low-cost repeatable deployment, or a complete production safety case.

## 2. Research timeline

| Stage | Main problem | Data and model change | Evaluation change | Remaining bottleneck |
|---|---|---|---|---|
| DYNA-1 / Dynamism v1 (2025-06-24) | Rare failures compound during long runs | Deployment data, progress estimation and a foundation reward model around a VLA | Company-run 24-hour napkin test and quality rubric | Unknown reset policy, adaptation cost and external validity |
| DYNA-1i / Open-World Dexterity (2025-10-15 index date) | In-task robustness does not guarantee environment transfer | Tens of hours of diverse office robot post-training | 30-min seen: 22 shirts, ~40/hr, ~3+ quality; 30-min combined unseen: 20 shirts, ~40/hr, ~3+; plus public demos | Only one disclosed window per condition; repeats, uncertainty and complete logs absent |
| DYNA-1 Pre-Training (2025-11-20) | Reduce environment- and task-specific data | Broad base-model pre-training; selected one-hour adaptation | Qualitative zero-shot tasks and near-100% internal claims | Corpus, attempts, contamination and task-level protocol undisclosed |
| DYNA-2 (2026-08-15) | Scale transferable representations beyond scarce robot data | 1M+ hours human video; pseudo-actions; joint future-video/action flow matching | Fixed human validation, 39 offline robot tasks, 14 physical tasks across three embodiments | No independent replication; robot post-training remains necessary |
| Dyna-2 infrastructure (2026-08-17) | Make million-hour experiments start, stream and recover repeatably | New container/codec, ingestion DAG, warehouse manifest, local cache, distributed optimizer and resilience layers | Internal systems before/after measurements | Cost, full configurations and reproducible artifacts undisclosed |

The detailed eight-question transition analysis is in [`dyna_research_timeline_v1.md`](../dyna/dyna_research_timeline_v1.md).

## 3. Twenty claims that define the current evidence base

| Claim | Evidence reading | Boundary |
|---|---|---|
| DYN-C005 — 99.4% DYNA-1 success | Quantified internal result | Denominator, retry policy and failure taxonomy incomplete |
| DYN-C010 — unseen customer environment | Reported zero-shot site entry | Zero-shot quality, throughput and trial count unknown |
| DYN-C061 — DYNA-1i seen benchmark | 22 shirts in one 30-minute company-run window; ~40/hr and ~3+ quality | No repeats, confidence interval or complete failure log |
| DYN-C062 — DYNA-1i combined unseen benchmark | 20 shirts in one 30-minute Lobby / Parking / CoRL row; ~40/hr and ~3+ quality | Per-environment allocation and repeats undisclosed |
| DYN-C018 — one-hour adaptation near 100% | Selected company demonstration | Which task used one hour and attempt counts undisclosed |
| DYN-C019 — 1M+ human-video hours | Corpus scale reported by Dyna | No dataset release, licensing inventory or independent audit |
| DYN-C020 — WAM architecture | Architecture and objectives are described | Parameters, code, weights and full recipe absent |
| DYN-C021 — no future-video inference | Explicit architectural statement | Production variants are not fully specified |
| DYN-C023 — 1k/10k/100k/1M ladder | Clear nested data-scale experiment | Model-size and compute scaling are not tested |
| DYN-C026 — 39-task offline suite | 12 internal + 27 external-source tasks | Scoring remains company-run; dataset preprocessing incomplete |
| DYN-C028 — offline cross-embodiment improvement | Four robot metrics reportedly improve monotonically | Prediction accuracy is not physical task success |
| DYN-C029 — 14 tasks, three embodiments | Physical evaluation has defined platform coverage | Tasks are internal and use task-specific robot post-training |
| DYN-C031 — 20→28→45→53 normalized score | Reported monotonic physical-suite gain | Small per-task trials; no seed/confidence reporting |
| DYN-C032 / C068 — bottle-cap adaptation | Technical report says roughly 10 minutes; corrected press release says 13; 10/10/40/50% across rungs | Ten trials per checkpoint; accepted-versus-collected duration unknown |
| DYN-C034 — joint objective wins 39/39 | Controlled internal action-only comparison | Effect sizes, seeds and independent reproduction absent |
| DYN-C035 — video-only data helps offline prediction | Isolates an important additional data axis | Not repeated as an on-robot ablation |
| DYN-C038 — customer-site 87% vs 46% | Matched internal model comparison at customer sites | Sites, tasks, trials and production criteria undisclosed |
| DYN-C043 — ingestion 14k→440k episode-hours/week | Concrete internal pipeline before/after metric | Hardware, cost, duplication and QA throughput incomplete |
| DYN-C045 — ~2 GB/s/node and 98% GPU utilization | Plausible warm-cache training measurements | Node count, utilization definition and cold-cache behavior absent |
| DYN-C057 — weeks to months of site engineering | Candid company retrospective directly qualifies earlier messaging | Deployment-level labor table and current trend not disclosed |

## 4. Most reliable public evidence

The best evidence is not necessarily the most commercially important evidence.

1. **Dyna-2's definition of the experimental variable.** Nested human-video subsets and the explicit statement that model-size and compute scaling are outside scope make the claim auditable in principle [DYN-C023, DYN-C024].
2. **The inference distinction.** The report clearly states that the disclosed policy does not use generated future video at inference, preventing an inflated “imagination planning” interpretation [DYN-C021].
3. **The physical-suite recipe boundary.** Dyna discloses three embodiments, 14 tasks, 10–12 trials, at most ten hours of robot data per task and robot-only post-training [DYN-C029, DYN-C030, DYN-C033].
4. **Objective and data ablations.** Action-only, joint video/action and video-co-training variants isolate parts of the WAM argument, although the experiments remain internal [DYN-C034, DYN-C035].
5. **Infrastructure before/after measurements.** Storage, ingestion, manifest, cache and optimizer metrics are concretely scoped and mostly avoid claiming end-to-end policy gains [DYN-C041–DYN-C047].
6. **The deployment retrospective.** A founder-authored statement that deployment scaling remained unsolved is first-party evidence against a favorable company narrative and therefore especially informative [DYN-C057, DYN-C058].
7. **Funding confirmation.** Bloomberg independently corroborates the $120M financing and a valuation above $600M [DYN-C059].

“Strong” in this audit means the public source states a method or boundary clearly. It does not mean the Dyna result has been independently reproduced.

## 5. Company claims requiring the most caution

- **24/7 autonomous and real-time recovery:** selected long runs and videos do not establish fleet uptime, intervention rate, maintenance burden or safety supervision [DYN-C051].
- **Deploy in minutes / learn in hours / scale from one site to hundreds:** Dyna's own retrospective reports weeks to months of on-site engineering for early deployments and says repeatable deployment scaling remained unsolved [DYN-C052, DYN-C057, DYN-C058]. “Zero custom engineering” is not retained as a current claim without a dated snapshot.
- **Homepage throughput and quality:** laundry, food and factory figures lack robot count, denominator, runtime, intervention and customer protocol [DYN-C048–DYN-C050].
- **87% customer-site production criteria:** customer sites and operators make the setting relevant, but Dyna does not disclose task mix, trial count, criteria or customer identities [DYN-C038].
- **99% Monster Laundry acceptance:** it is a named customer testimonial, not an independent log audit; attendants remain part of the workflow [DYN-C053, DYN-C054].
- **Human-to-robot scaling law:** the evidence is an internal ladder across held-out prediction and selected post-trained tasks, not a universal law across model size, compute, datasets and embodiments [DYN-C028, DYN-C031].

## 6. Dyna-2's main scientific contributions

1. A large, nested human-video scaling experiment centered on robot-transfer measurements rather than only human-video reconstruction.
2. A pseudo-action representation derived from 3D wrist trajectories and thumb–index aperture that allows human videos to train an action objective.
3. A joint future-video and future-action flow-matching architecture with modality-specific transformer streams.
4. A clear demonstration, within Dyna's internal protocol, that world-model training can help an action policy even when future video is not generated at inference.
5. A video-only scaling ablation suggesting that examples lacking reliable pseudo-actions can still improve offline robot prediction.
6. A multi-level evaluation spanning human validation, zero-shot offline robot prediction and matched robot-only post-training across three embodiments.

These contributions should be described as **company-reported research results**, not as peer-reviewed or independently replicated findings.

## 7. Dyna-2 infrastructure's main engineering contributions

The engineering report treats robot foundation-model training as a coordinated data system rather than a GPU procurement problem. Its most transferable ideas are:

- encode multi-camera streams with H.264 inside timestamp-aware MCAP containers and tune topic-group chunks around temporal access patterns;
- decompose ingestion into observable Airflow tasks instead of one monolithic Kubernetes job, then stagger write-heavy starts and bin-pack work;
- move metadata truth into a transactional database, propagate changes into a warehouse and materialize compact, memory-mapped manifests;
- place an Alluxio-managed NVMe cache near training nodes so repeated random temporal reads do not depend on remote object storage;
- change optimizer sharding with network topology so frequent communication stays on faster intra-node links;
- use preflight gating, automatic restart, requeue and checkpoint recovery to bound the cost of long multi-node failures.

The detailed concept-by-concept explanation is in [`dyna_2_infrastructure_deep_dive_v1.md`](../dyna/dyna_2_infrastructure_deep_dive_v1.md).

## 8. Largest evidence gaps

1. No released Dyna-2 dataset inventory, weights, code, training compute, model size or complete hyperparameters.
2. No independent replication, peer review or third-party execution of the disclosed evaluations.
3. No full leakage and deduplication audit between the million-hour corpus, task semantics, external datasets and evaluation environments.
4. Limited statistical reporting: no multiple seeds, per-task confidence intervals or power-law fit with uncertainty.
5. No zero-shot **physical** cross-embodiment suite; the zero-shot result is offline action prediction.
6. No on-robot video-only scaling ablation and no public comparison against strong external VLA/WAM baselines under a shared protocol.
7. Task-specific robot post-training cost omits teleoperation, curation, failed collection, engineering and hardware setup.
8. Customer deployment lacks fleet-level runtime, intervention, recovery, maintenance, energy and total-cost data.
9. Safety disclosures do not cover force limits, emergency stops, human detection, sensor drift, hardware wear, network faults or incident reporting.
10. Current customer count, employee count, hardware unit economics and RaaS economics are not reliably public at the cutoff.

## 9. Dyna and Sunday: core route differences

| Dimension | Sunday Robotics | Dyna Robotics |
|---|---|---|
| Initial target | Unstructured household work | Repetitive commercial, industrial and service workflows |
| Primary data proposition | Memory Glove and human demonstrations closely tied to household manipulation | Egocentric human video at very large scale plus pseudo-actions and task-specific robot data |
| Model emphasis | Founder research lineage includes ALOHA, ACT, Mobile ALOHA, Diffusion Policy and UMI; Sunday ACT-1/ACT-2 are proprietary and their complete architectures are not public | VLA/reward-model lineage moving to a joint World-Action Model; the current integrated stack also names VLM and low-level control modules |
| Embodiment pressure | General home embodiment operating around people and deformable objects | Stationary bimanual arms, dexterous hands and semi-humanoid research platforms; narrow production stations first |
| Scaling hypothesis | Better human demonstration capture and task-relevant interaction data can unlock household skills | Human-video scale can improve transferable representations; deployments supply the robot post-training loop |
| Deployment objective | Broad household usefulness and home safety | Measurable production throughput, quality and continuous operation |
| Dominant current evidence gap | Breadth, recovery and safety across real homes | Independent scaling replication and repeatable low-engineering customer deployment |

This comparison is conceptual, not a shared benchmark. The companies disclose different tasks, embodiments, metrics and maturity signals, so their percentages should not be ranked directly.

## 10. Recommended information architecture for a future `Dyna.html`

Do not publish a long narrative as a single undifferentiated page. A useful Dyna research page should expose evidence boundaries at the point each claim appears.

1. **Executive evidence card** — thesis, research cutoff, confidence legend and five conclusions.
2. **Company thesis and physical economy** — why Dyna starts with commercial stationary workflows; distinguish stated strategy from demonstrated economics.
3. **Research lineage** — founders, reward-learning lineage, DYNA-1 → DYNA-1i → pre-training → DYNA-2.
4. **System and embodiment map** — DYNA-VLM → DYNA-2 → DYNA-System0 → DYNA-SAUR, plus stationary bimanual YAM, WUJI-2 hands and semi-humanoid evaluations; separate module attribution and train/evaluate/demo roles.
5. **Data engine** — human video provenance, hand-pose filtering, pseudo-action construction, video-only data and robot post-training data.
6. **Dyna-2 model** — WAM architecture, objectives and the explicit boundary between world-model training and imagined-video planning.
7. **Scaling-law audit** — independent variables, fixed variables, 1k/10k/100k/1M ladder, human metrics, 39-task offline results and statistical limits.
8. **Post-training and adaptation** — task/embodiment specificity, robot hours, bottle-cap case and unreported engineering cost.
9. **Infrastructure deep dive** — storage → ingestion → catalog/manifest → delivery/cache → distributed optimizer → resilience, with robotics-specific annotations.
10. **Demo and deployment ledger** — laundry, hospitality, factory and conference examples labeled demo, internal evaluation, customer case or production claim.
11. **Reliability and safety** — recovery evidence, intervention accounting and an explicit Unknown table for long-run hazards and safety controls.
12. **Business and funding** — dated financing, investors, RaaS indication, named customers and unknown unit economics.
13. **Claim explorer** — filterable registry showing source type, evidence strength, external corroboration, independent verification, claim importance, verification risk and fix priority.
14. **Open research questions** — missing ablations, reproducibility, deployment scaling, safety and cost.
15. **Sources and change log** — stable source IDs, accessed dates, link status, version changes and corrections.

Before building this page, manually verify the ten P0 claims listed in the project handoff, archive the key official pages, and decide whether the page should embed compact registry views or load them from versioned JSON generated from the CSV files. No `Dyna.html` is created in this audit version.

## Evidence interpretation

- **Author directly showed:** a result with a disclosed internal experiment and measurable output.
- **Author explanation:** a causal or mechanistic interpretation proposed by Dyna, even when compatible with the data.
- **Company marketing:** a product or deployment statement without a sufficient public evaluation protocol.
- **This report's inference:** analysis connecting sources; it is not presented as a Dyna result.

The registries preserve these boundaries at claim level. Unknown values remain `Unknown` rather than being inferred from “production-grade,” “autonomous” or “foundation model” language.

### Corroboration versus verification

`external_corroboration` records evidence adjacent to a claim: an external dataset, public event, named customer, independent report or investor confirmation. It does not establish that the third party executed or audited Dyna's evaluation. `independent_verification` is `Yes` only when a third party independently verifies the specific claim; `Partial` is reserved for limited independent confirmation. Consequently, ABC dataset provenance, CoRL demonstrations and Dyna-hosted customer testimonials are **not** independent verification.

The phrase “two-loop strategy” used in the timeline is explicitly labeled **Research Inference**. It is this audit's synthesis of broad human-video pre-training plus narrow deployment-fed robot post-training, not a public Dyna term.
