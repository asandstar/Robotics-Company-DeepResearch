# Dyna Robotics Research Timeline v1

**Research cutoff:** 2026-08-18  
**Evidence rule:** dates and results below describe what Dyna publicly reported. Except where noted, they are not independent replications or peer-reviewed Dyna papers. Source IDs resolve to [`data/dyna_source_registry_v1.csv`](../../data/dyna_source_registry_v1.csv).

## Technical summary

Dyna's public route moves through three successive bottlenecks. DYNA-1 focuses on keeping a narrow commercial task running through rare failures; DYNA-1i and the DYNA-1 pre-training update focus on environment transfer and low-data adaptation; DYNA-2 shifts the main scaling resource from robot deployment data to human egocentric video, while retaining task-specific robot post-training. The infrastructure report then explains what had to change to make one-million-hour experiments repeatable.

This is not a clean replacement sequence. The 2026 deployment retrospective says pre-training alone did not solve production deployment: early customer installations still required weeks to months of on-site engineering, and knowledge did not yet transfer cleanly from one deployment to the next [DYN-S012]. **Evidence label — Research Inference:** this audit summarizes the public route as a two-loop strategy—broad human-video pre-training plus narrow, deployment-fed robot post-training. Dyna does not publicly name it a “two-loop strategy.”

## 1. Dynamism v1 / DYNA-1 — reliability before breadth

**Public milestone:** 2025-06-24 [DYN-S003]

1. **What it solved:** Dyna targeted compounding-error failure in long-horizon commercial manipulation. The disclosed example was a company-run 24-hour napkin-folding test: 850+ folds, 99.4% reported task success and zero reported interventions.
2. **What bottleneck it exposed:** a policy that succeeds once is not necessarily a policy that can recover after hundreds of unusual states. The company also disclosed that only 75% of folds reached its grade-4-or-5 production threshold even though 98% reached grade 3 or better.
3. **What changed next:** DYNA-1i concentrated on keeping the learned skill robust under visual and environmental change, not merely in the original station.
4. **Data scale:** not publicly quantified. Dyna describes an RM-in-the-loop process using autonomous runtime experience and automatic segmentation of continuous streams.
5. **Architecture:** described as a VLA plus a foundation reward model; model size, backbone, action representation and training recipe were not disclosed.
6. **Evaluation:** a long continuous internal run and qualitative failure-recovery examples. The lack of a public attempt log, reset policy and independent observer limits the result.
7. **Deployment:** Dyna said it had paying napkin-folding customers. It also acknowledged that zero-shot customer-site quality and throughput dropped and improved only after on-site training.
8. **Scaling hypothesis:** repeatable competence comes from a reward model that turns deployment failures and progress states into training signal. Scale meant more experience inside a task and better recovery, not yet broader pre-training.

## 2. Open-World Dexterity / DYNA-1i — separate robustness from environment generalization

**Index milestone:** 2025-10-15; the page body displays “December 2025,” so the registry preserves the discrepancy [DYN-S002, DYN-S004].

1. **What it solved:** Dyna explicitly separated on-task robustness from cross-environment generalization. Beyond selected videos, it reported a 30-minute seen-office result (22 shirts, about 40/hour, average quality about grade 3+) and one 30-minute combined unseen Lobby / Parking / CoRL result (20 shirts, about 40/hour, average quality about grade 3+) after training on a handful of office stations [DYN-S004].
2. **What bottleneck it exposed:** the original DYNA-1 could remain robust within a task yet degrade outside the training environment. “99%+” in-distribution performance therefore did not imply open-world deployment.
3. **What changed next:** the following pre-training update moved part of generalization into the base model, aiming to reduce task- and environment-specific robot data.
4. **Data scale:** “tens of hours” of robot post-training collected in the office. Exact hours, number of stations, shirts, configurations and rejected runs were not given.
5. **Architecture:** still described as an improved DYNA-1 VLA recipe. The public post does not isolate whether gains came from data diversity, augmentation, model changes or training objective.
6. **Evaluation:** the company publishes two quantitative condition rows, each defined as a 30-minute continuous window counting shirts folded consecutively without failure. The page does not disclose independent repeats, confidence intervals, a full failure log, or how the combined unseen window is divided among Lobby, Parking and CoRL. CoRL also appears separately as a three-day public demo, not as independent verification.
7. **Deployment:** live conference demonstrations provide external corroboration that a system was publicly shown, but are not a controlled reliability study; staff presence, active runtime, resets and failures are unknown.
8. **Scaling hypothesis:** modest station diversity plus a robust task policy may be enough to bridge selected environment shifts. The evidence did not yet establish unrestricted open-world generalization.

## 3. DYNA-1 Pre-Training — capability in the base, then one-hour adaptation

**Public milestone:** 2025-11-20 [DYN-S005]

1. **What it solved:** the company reported a base model that performed selected laundry and package-sorting behaviors without task-specific post-training.
2. **What bottleneck it exposed:** the update was short and omitted the corpus, architecture, attempts, contamination controls and task-level denominators. It demonstrated a direction, not a reproducible scaling law.
3. **What changed next:** DYNA-2 made data scale, objectives and evaluation rungs explicit and introduced an offline cross-embodiment measurement.
4. **Data scale:** undisclosed pre-training corpus. New-task adaptation was reported as “as little as one hour” of demonstrations.
5. **Architecture:** still labeled DYNA-1; no new model specification was published.
6. **Evaluation:** selected stress-test tasks—cup stacking, celery chopping, breakfast making and tablemat preparation—with “roughly 100%” reported success. It is unclear which task used one hour or how many attempts were counted.
7. **Deployment:** no production deployment protocol was disclosed for these particular tasks.
8. **Scaling hypothesis:** broad pre-training can create zero-shot dexterity and improve the sample efficiency of task-specific robot fine-tuning.

## 4. DYNA-2 — human-video scale as the pre-training axis

**Public milestone:** 2026-08-15 [DYN-S006]

1. **What it solved:** Dyna-2 supplied a four-rung 1k → 10k → 100k → 1M-hour ladder spanning three orders of magnitude and tested whether human-video scaling improves both held-out human action prediction and held-out robot action prediction.
2. **What bottleneck it exposed:** action-labeled human video is expensive because 3D hand tracks must pass a quality bar. Video without acceptable pseudo-actions grows faster than labeled data. Offline prediction also remains separated from physical success.
3. **What changed:** Dyna moved from a VLA-centered description to a World-Action Model trained on future-video and future-action objectives. It used video-only examples to scale the world-modeling objective beyond the pseudo-action subset.
4. **Data scale:** more than 1M hours of predominantly head-mounted human manipulation video. Nested subsets preserve source proportions. A separate fixed 100-hour human validation set is used. Licensing, corpus composition and deduplication are undisclosed.
5. **Architecture:** video-diffusion backbone; modality-specific DiT streams in a mixture of transformers; flow matching; a shallower action transformer joined to early video layers. In the scaling-law variant, the policy does **not** generate or attend to future video at inference—the future-video loss shapes a shared representation during training.
6. **Evaluation:** four held-out human metrics; a 39-task zero-shot offline robot suite (12 internal, 27 external ABC tasks); and 14 on-robot post-training tasks across three embodiments. The 14-task suite uses 10 trials per task and 12 for language following.
7. **Deployment:** the company reports 87% versus 46% customer-site pass rates for Dyna-2 versus Dyna-1 under matched task post-training and no site data. Sites, task mix, attempts and criteria are not public.
8. **Scaling hypothesis:** future-video prediction creates a representation that transfers across embodiments, and human video— including video without action labels—is a scalable pre-training resource. Robot data remains necessary for the reported physical task policies.

## 5. Dyna-2 Infrastructure — make the million-hour rung repeatable

**Public milestone:** 2026-08-17 [DYN-S007]

1. **What it solved:** the team reports that storage, ingestion, manifest creation, data delivery, optimizer communication and job failures each became the limiting factor at different scales.
2. **What bottleneck it exposed:** more GPUs could not fix slow random reads, scheduler write bursts, tens of millions of metadata lookups, remote object-store latency or multi-week job failures.
3. **What changed:** HDF5/per-frame JPEG gave way to tuned MCAP/H.264 and topic-group chunking; a monolithic Kubernetes job gave way to Airflow DAGs; filesystem crawling gave way to warehouse queries and memory-mapped manifests; cluster-local NVMe caching insulated training from remote storage; topology-aware optimizer sharding kept communication inside nodes; preflight gating and checkpoint restart reduced failure cost.
4. **Data scale:** the article describes 43M episodes at the one-million-hour manifest stage and later more than 50M rows; the corpus is petabyte scale.
5. **Architecture change:** this stage changes the data/training system rather than the policy architecture.
6. **Evaluation:** internal before/after engineering measurements: ~68% smaller storage, ~2.9× faster reads, 14k → 440k episode-hours/week ingestion, ~48 hours → under one minute to first batch, ~2 GB/s per-node cache reads, 98% warm-run GPU utilization and ~3× faster optimizer step at the switching scale.
7. **Deployment:** this is research-training infrastructure, not customer robot deployment. It improves experiment throughput and repeatability but does not establish robot reliability.
8. **Scaling hypothesis:** usable scaling is vertical and systems-bound. Dataset size is valuable only if experiments can be curated, started, fed and recovered repeatedly.

## How Dyna's scaling hypothesis changed

```text
DYNA-1
deployment experience → reward/progress model → recovery → longer task runtime

DYNA-1i / DYNA-1 pre-training
station and task diversity → environment transfer → less robot adaptation

DYNA-2
human pseudo-actions + video-only future prediction → cross-embodiment representation
→ matched robot post-training → broader physical task performance

Dyna-2 infrastructure
repeatable storage + ingestion + curation + delivery + resilient training
→ faster scientific iteration at million-hour scale
```

The unresolved question is not whether more human video improves Dyna's disclosed offline metrics—it does in the company report—but how much of that improvement survives new tasks, new embodiments, independent benchmarks and customer production after counting robot data, engineering time, intervention and maintenance.
