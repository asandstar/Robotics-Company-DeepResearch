# Dyna Marketing Language vs Technical Mechanism v1

**Research cutoff:** 2026-08-18  
**Purpose:** prevent wording from Dyna's homepage and press releases from being silently promoted into a stronger scientific statement. Source IDs resolve to [`data/dyna_source_registry_v1.csv`](../../data/dyna_source_registry_v1.csv).

## Technical summary

Dyna's marketing language is often directionally connected to the technical report, but it operates at a higher abstraction level. The main correction is not to label every phrase false; it is to translate each phrase into the narrow mechanism and evaluation actually disclosed.

Two distinctions dominate the audit:

1. **Data source is not supervision type.** Raw training input can originate entirely from human video while a hand-pose estimator converts some clips into wrist and grasp pseudo-actions. “Pure video source” therefore does not mean “no action supervision.”
2. **World-model training is not necessarily planning by imagination.** Dyna-2 predicts future video during training, but the disclosed scaling-law action policy neither generates nor attends to predicted future video at inference.

## Phrase-level audit

| Marketing wording | Technical mechanism | Supported interpretation | Unsupported interpretation | Source ID | Risk of misreading |
|---|---|---|---|---|---|
| **pure video** | Raw pre-training examples originate as human videos. Clips that pass a 3D hand-pose quality bar produce wrist trajectories and grasp-aperture pseudo-actions; other clips can train only future-video prediction. | Robot teleoperation trajectories are not used in the disclosed scaling-law pre-training, and video-only examples add a second scaling axis. | There is no action-like supervision, annotation pipeline, pose estimation or robot post-training anywhere in the system. | DYN-S006, DYN-S033 | High |
| **powered entirely by human egocentric video** | The pre-training corpus is predominantly head-mounted human manipulation video; robot policies in the physical suite are subsequently post-trained on task-specific robot demonstrations. | The pre-training checkpoint's disclosed data source is human video rather than robot trajectories. | The final physical policies are trained entirely without robot data, calibration or embodiment-specific adaptation. | DYN-S006, DYN-S033 | High |
| **zero-shot** | Dyna uses the term for several different boundaries: no task/environment-specific post-training in a DYNA-1 base-model update; no evaluation-source robot trajectories in Dyna-2 pre-training; and no customer-site data in one site comparison. | Zero-shot can be valid when its excluded data and stage are stated explicitly. | Zero-shot offline robot prediction equals zero-shot physical control; or no site data means no prior task post-training or deployment engineering. | DYN-S003, DYN-S005, DYN-S006, DYN-S033 | High |
| **production-grade** | Dyna applies internal or customer quality bars to task outputs and reports selected long-run, throughput and customer-site results. | The system was designed and internally evaluated against a production-oriented quality criterion. | Safety certification, fleet-wide uptime, low maintenance, zero intervention, repeatable unit economics or third-party acceptance have been established. | DYN-S001, DYN-S003, DYN-S006 | High |
| **world model / World-Action Model** | Dyna-2 jointly learns future-video and future-action flow-matching objectives through modality-specific transformer streams. | Future prediction is an explicit training objective, and company ablations report benefits over action-only training. | Every module in the deployed robot is part of Dyna-2; or the term itself establishes physical reasoning, planning or causal understanding. | DYN-S001, DYN-S006 | Medium |
| **imagines the future, then acts** | The homepage compresses a training story into product language. The disclosed scaling-law action policy does not generate or attend to predicted future video at inference; a separate one-step video student is demonstrated without showing that it drives the reported policy results. | Future-video prediction can shape representations used for action. | The reported robot success was produced by online candidate-future generation and planning through imagined video. | DYN-S001, DYN-S006 | High |
| **human-to-robot scaling law** | Human-video hours vary across a four-rung 1k/10k/100k/1M ladder spanning three orders of magnitude. Dyna reports monotonic held-out human and offline robot prediction trends plus a matched post-trained physical suite. | Within Dyna's fixed model family and company-run protocol, more human-video pre-training correlates with better disclosed robot-transfer metrics. | A universal law has been established across model size, compute, robot-data scale, unrelated embodiments and production deployments. | DYN-S006, DYN-S033 | High |
| **10-minute / 13-minute bottle-cap data** | The technical report rounds the task-specific robot demonstrations to roughly ten minutes; the corrected press release states 13 minutes of data. The physical result uses ten trials per checkpoint under the suite protocol. | The example uses a small amount of task-specific robot data; 13 minutes is the more precise press-release wording, while roughly ten is the technical-report summary. | The collection required only ten minutes of total operator and engineering time, or the result is statistically stable across seeds and bottle variations. | DYN-S006, DYN-S033 | High |
| **14-task / 15-task physical evaluation** | The earlier company press release says 15 benchmark tasks; the later, more detailed technical report defines and enumerates a 14-task physical suite. | Scientific analysis should use the technical report's canonical 14-task definition while preserving the earlier press-release wording as a source-version discrepancy. | The discrepancy's cause is known, or the two counts can be reconciled by assuming a task was removed, merged, renamed or counted differently. | DYN-S006, DYN-S033 | High |

## Current homepage language

As accessed for the 2026-08-18 cutoff, the current homepage supports these product-positioning phrases [DYN-S001]:

- learning new skills in hours;
- deploying in minutes;
- scaling from one site to hundreds;
- production-grade performance;
- real-time recovery;
- 24/7 autonomous operation;
- specified laundry, hospitality and factory workflow metrics.

The current page version examined for this correction does **not** provide a dated, immutable snapshot supporting “zero custom engineering.” Search indexing showed that wording in a previously crawled homepage variant, but a search-engine cache is not a dated archive record. The phrase is therefore removed from the active claim rather than retained as a current fact or labeled historical without an archive.

## System-level attribution

The current homepage separates DYNA-VLM, DYNA-2, DYNA-System0 and DYNA-SAUR [DYN-S001]. This makes a second marketing-to-technical translation necessary: an integrated robot video may demonstrate the full stack, not Dyna-2 alone. High-level task reasoning, low-level control, navigation, sensing, safety interlocks and workflow orchestration must remain unattributed unless the company identifies the responsible module or publishes an ablation.

## Editorial rules for a future Dyna report

1. Attach a qualifier directly to every use of “zero-shot.”
2. Write “human-video pre-training” rather than “video-only policy” when robot post-training follows.
3. Use “world-model training objective” unless inference-time imagined planning is actually demonstrated.
4. Label homepage throughput and autonomy figures as current company claims with unknown denominators.
5. Use the exact source-specific bottle-cap wording rather than collapsing 10 and 13 minutes into false precision.
6. Attribute integrated-system behavior to the full Dyna stack unless an experiment isolates Dyna-2.
7. Use 14 tasks for the physical suite and retain 15 tasks only as earlier press-release wording; do not speculate about the source-version discrepancy.
