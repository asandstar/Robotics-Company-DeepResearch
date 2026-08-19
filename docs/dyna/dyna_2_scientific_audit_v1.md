# Dyna-2 Scientific Audit v1

**Research cutoff:** 2026-08-18  
**Object:** Dyna Robotics, “Dyna-2: A 1-Million-Hour Scaling Law for World-Action Models,” 2026-08-15 [DYN-S006].  
**Review status:** company technical report, not a peer-reviewed paper; no public code, weights or training corpus located as of cutoff.

## Technical summary

Dyna-2 presents unusually large and comparatively well-structured first-party evidence for human-video pre-training in robot learning. Its strongest result is narrow but important: with architecture and training configuration held fixed, four nested human-video budgets rank monotonically on held-out human action prediction and on company-run offline prediction over held-out robot trajectories. Objective ablations further indicate that future-video prediction—not action data alone—is necessary for the reported cross-embodiment trend.

The work does **not** establish that human video alone yields autonomous robot policies. The physical 14-task results require task-specific robot demonstrations, and the customer-site comparison lacks enough task/site/trial disclosure for independent audit. “World model” here primarily describes a training objective; the disclosed scaling-law policy remains reactive and does not plan by generating future video at inference.

Overall assessment: strong hypothesis generation and meaningful controlled internal experiments; promising but unreproduced scientific evidence; insufficient disclosure for a field-level scaling law or production-reliability conclusion.

## Research question and claimed contribution

The report asks:

1. Does performance scale with 1k→1M hours of human manipulation video?
2. Does the same ordering appear on robot data excluded from pre-training?
3. Does that ordering survive task-specific robot post-training and physical evaluation?
4. Which objective/data component causes cross-embodiment transfer?

The central scientific contribution is the conjunction of scale and objective: a video/action co-trained WAM shows monotonic prediction gains across a four-rung ladder spanning three orders of magnitude, while controlled ablations report that future-video prediction enables the cross-embodiment trend and video-only data adds a usable scaling axis.

## Model specification

The disclosed model is a video-diffusion-based mixture of transformers. Video and action are separately tokenized and processed through distinct DiT stacks that exchange information; proprioception enters the action transformer. Video tokens use causal masking; action tokens use bidirectional self-attention and observed video context. Text cross-attends into video, not directly into action. Training uses flow matching over future-video latents and future action chunks.

For the scaling-law variant, video and action losses share a trunk but fit separate marginal velocity fields. The action predictor does not consume the noisy future-video latent. At inference, it neither generates nor attends to predicted future video. This is scientifically important: the result supports **representation learning through future prediction**, not **planning through imagined rollouts**.

Missing model details include parameter count, video encoder/latent codec, text backbone, tokenizer sizes, context/horizon, control rate, action parameterization, normalization, training tokens/steps, optimizer settings, batch composition, compute, random seeds and latency.

## Experimental variables and controls

### Main ladder

- Independent variable: nested human-video hours—1k, 10k, 100k, 1M.
- Intended controls: source proportions, model architecture, training and evaluation configurations.
- Held-out human set: fixed disjoint 100 hours.
- Human metrics: MSE, L1 and accuracy at two normalized thresholds.
- Checkpoint handling: mean and standard deviation over 10 late-window checkpoints.

This is stronger than comparing separately curated datasets because larger rungs only add data. It still leaves open whether each rung receives the same updates, epochs, token budget or compute. “Hours” mixes frame rate, clip redundancy, task diversity and annotation quality.

### Objective/data ablation

At fixed 5k, 50k and 100k action-labeled human hours, Dyna compares:

- action-only;
- joint action and future-video prediction on the same data;
- joint training plus an equal amount of additional video-only data.

The joint recipe reportedly beats action-only on all 39 offline robot tasks at each scale. Only the video-co-trained condition improves with action scale. A second ablation fixes action-labeled data at 50k or 250k hours and scales video-only data. Both regimes reportedly show monotonic held-out robot improvement.

These are the most diagnostic experiments in the report. However, extra video changes total tokens/compute unless training is carefully budget-matched; the report does not provide enough compute accounting to separate objective from additional optimization exposure.

## Scaling-law definition

Dyna uses “scaling law” for monotonic, power-law-like improvement in prediction metrics as human-video hours increase. On held-out human data, the report says all four metrics fit power laws; on robot data, it emphasizes monotonic ranking and a 10k→100k inflection.

Important limits:

- Four main data points are a thin basis for estimating an exponent or extrapolating beyond 1M hours.
- No fitted coefficients, residual plots, goodness-of-fit comparison, confidence interval over exponent or out-of-sample scaling prediction are accessible in the prose.
- Model size and compute are explicitly not varied.
- Offline robot metrics measure action prediction, not task completion.
- The 14-task physical result is monotonic after post-training but is a heterogeneous normalized score, not the same metric as offline loss.

Recommended wording: “Dyna reports monotonic data-scaling trends across four human-video budgets, consistent with a scaling-law hypothesis,” rather than “Dyna proved a universal human-to-robot scaling law.”

## Evaluation design

### Held-out human data

Strengths: fixed disjoint validation set; continuous and thresholded metrics; checkpoint averaging; nested training sets. Weaknesses: source composition and leakage audit are not public; checkpoint samples are correlated and do not replace independent training seeds.

### 39-task offline robot evaluation

The suite combines 12 internal tasks and 27 tasks from the external xdof ABC source, on two stationary bimanual YAM platforms [DYN-S023]. No evaluated trajectory is said to appear in pre-training. Tasks cover cloth, knots, packing, cleaning, food service and assembly.

Using an external dataset reduces task-design bias, but evaluation is still run by Dyna. The report does not disclose trajectory counts per task, preprocessing, task weighting, confidence intervals, leakage checks against semantically similar human clips or whether thresholds were chosen before seeing results.

“Zero-shot” applies to robot data in **pre-training** and to offline prediction. It should not be used to describe the 14-task physical policies, which are post-trained on robot demonstrations.

### 14-task on-robot post-training evaluation

Embodiments:

- 11 tasks: two 6-DOF YAM arms with parallel-jaw grippers;
- 2 tasks: the same arms with paired WUJI-2 20-DOF hands;
- 1 language task: early semi-humanoid prototype.

Each task uses at most 10 hours of robot data. The company reports 10 trials per task and 12 for language following, with evaluators separated from model development. Native metrics are normalized to each task's maximum and averaged equally. Mean normalized score grows 20%→28%→45%→53% across the pre-training ladder.

Strengths: matched post-training recipe; multiple capabilities; physical trials; explicit task-specific metrics; evaluator-role separation. Weaknesses: low trial counts; heterogeneous metrics; internal task selection; no confidence interval on aggregate trend; no independent operator; unclear resets/randomization; no report of all seeds or failed training runs.

### Customer-site evaluation

Dyna reports 87% production-pass rate for Dyna-2 versus 46% for Dyna-1, with the same task data and steps and no site data. Operators not involved in model development reportedly applied customer acceptance criteria.

This is potentially valuable external-distribution evidence, but the missing denominator, sites, tasks, acceptance rules, operator affiliation and uncertainty prevent strong inference. “Operator not involved in model development” is not synonymous with independent third-party verification.

## Data leakage and benchmark construction risks

- Exact robot trajectories are excluded, but the human corpus may contain the same task semantics, objects or environments.
- Data partners and corpus licenses are undisclosed, preventing duplication checks.
- The internal 12 tasks may have influenced architecture and metric selection through earlier experimentation.
- External ABC trajectories reduce but do not eliminate benchmark adaptation; Dyna could still tune on aggregate performance.
- A fixed 100-hour human validation set can become an implicit development set if repeatedly evaluated during the project.
- Customer-site “unseen” status depends on whether related site images, objects, instructions or deployment data entered any pipeline.

A credible leakage audit would publish hashes/semantic-neighbor analysis across sources, freeze dates, define prohibited data and separate development from final test suites.

## Checkpoint selection and statistical reporting

The main human ladder averages 10 late-window checkpoints, reducing cherry-picking within a run. The WAM/VLA comparison uses three pre-training checkpoints per architecture and paired bootstrap 95% intervals over 21 task×checkpoint cells. These are positive practices.

Remaining issues:

- Checkpoints from one run are correlated and are not independent seeds.
- The physical ladder's per-task trial counts yield coarse percentages; bottle-cap 40% versus 50% can be one trial.
- No multiple-comparison correction is described for 39 tasks and multiple metrics.
- Power-law fit uncertainty and sensitivity to the metric threshold are not fully reported.
- “Best on 9 of 14 tasks” can be unstable without intervals.
- It is unclear whether failed runs/checkpoints were excluded and how hyperparameters were chosen.

## Cross-embodiment claim

### What is directly shown

- Human-video scale orders offline predictions on held-out robot trajectories from two YAM-based bimanual systems.
- After matched robot post-training, pre-training scale orders a 14-task normalized physical score across three disclosed embodiments.
- No human-robot alignment or co-training is used in the 14-task recipe.

### What is interpretation

Dyna attributes the transfer to world modeling: future prediction creates physical representations that are less tied to human pseudo-actions. The objective ablation supports this interpretation within the disclosed evaluation.

### What is not shown

- Transfer to embodiments outside the related arms/hands/semi-humanoid set.
- Physical zero-shot execution without robot post-training.
- Elimination of kinematic/action-space adaptation.
- Independent replication or a released benchmark result.

## WAM versus VLA comparison

Dyna compares an early Dyna-2 WAM with its production Dyna-1 VLA under matched pre-/post-training data and hyperparameters, using three pre-training checkpoints across seven robot tasks. It reports 1.55× pooled success rate, 1.12× pooled quality grade and 65/29/6% win/loss/tie cells.

Positive aspects: matched internal baseline; multiple checkpoints; paired bootstrap intervals; explicit admission that the pipeline had been tuned for VLA, potentially disadvantaging WAM.

Limitations: the VLA is only Dyna's internal architecture, not OpenVLA, π0 or another reproducible external baseline; task identities and raw counts are not fully exposed in accessible text; early WAM differs from final Dyna-2; tuning budgets may be asymmetric; the result cannot establish general WAM superiority.

## Post-training fairness

The four pre-training rungs receive the same task dataset and recipe, which is the correct direction. Yet fairness also requires identical hyperparameter search, stopping rule, seed count, checkpoint selection and training compute. These are not disclosed. Higher-scale representations may converge at different rates, so identical steps are a defensible control but not necessarily each model's best attainable performance.

The bottle-cap example is compelling for sample efficiency but fragile statistically: the technical report says roughly 10 minutes of robot demonstrations, while the corrected company press release says 13 minutes of data [DYN-S006, DYN-S033]. Ten evaluation trials per checkpoint do not reveal total collection effort, accepted-versus-recorded duration, demonstrator corrections, object diversity or repeatability across seeds.

## External benchmark usage

The 27 external ABC tasks add breadth and reduce the chance that every task was designed around Dyna's model [DYN-S023]. Still, Dyna's report is not an externally administered leaderboard. A stronger design would pre-register the evaluation, use untouched hidden trajectories, report results through the dataset maintainer or release prediction files and code for independent scoring.

## One-step video generation

The report presents a one-step student that reduces time-to-latents by ~90× versus the teacher, with an explicit admission that quality remains below the teacher. The method is scientifically separate from the scaling-law robot policy. It may make video planning/evaluation practical, but no experiment shows improved robot success from using the one-step generator in the control loop. Treat it as a promising generative-model contribution, not proof of imagined-video planning.

## Reproducibility and missing ablations

High-priority missing items:

1. Release or third-party audit of corpus hours, source mix, licenses, deduplication and hand-pose yield.
2. Model size, compute, batch/tokens, optimizer, seeds, context/action horizon and inference latency.
3. Random-seed replications for every scaling rung.
4. Compute-matched ablation for extra video-only data.
5. Frozen-representation probes versus full co-training to locate where transfer emerges.
6. Same-object/same-task semantic leakage analysis between human corpus and robot tests.
7. External hidden benchmark evaluation administered by maintainers.
8. More unrelated embodiments and action spaces.
9. Robot-data scaling curves at each pre-training rung.
10. Multi-task versus task-specific post-training and catastrophic-interference tests.
11. Explicit planning-by-video ablation versus reactive action inference.
12. Safety, intervention, recovery latency and hardware-failure metrics during physical tests.
13. End-to-end cost curve: human-video collection/annotation, GPU compute, robot data and engineer time.

## Four evidence layers

### Author directly demonstrates within the report

- Nested data-ladder design and disclosed objective equations.
- Company-run monotonic metrics on held-out human and robot datasets.
- Company-run objective and video-only data ablations.
- Company-run physical evaluation after robot post-training.

### Author interpretation

- World modeling is the causal reason for cross-embodiment transfer.
- A 10k→100k inflection represents emergence from coverage.
- The result generalizes to embodiments not studied.

### Company marketing language

- “Proves” the first human-to-robot transfer scaling law.
- “Zero-shot production-grade performance.”
- WAM “thinks in video,” imagines what happens next, then acts.

### This audit's inference

- The evidence strongly motivates future-video objectives for cross-embodiment representation learning.
- The disclosed policy is reactive at inference, so the WAM advantage cannot be attributed to online visual imagination.
- Physical usefulness still depends on robot post-training and a costly deployment loop.
- Independent replication is required before treating the scaling relationship as universal.

## Reviewer recommendation

If submitted as a paper with the current public material, the work would merit serious interest but require major revision for reproducibility, statistical completeness and claim calibration. The highest-value next step is not a larger marketing milestone; it is an independently administered evaluation with frozen checkpoints, released prediction artifacts and transparent robot-data/adaptation accounting.
