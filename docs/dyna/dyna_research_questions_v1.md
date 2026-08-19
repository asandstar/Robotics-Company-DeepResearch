# Dyna Robotics Technical Question Map v1

**Cutoff:** 2026-08-18 · **Purpose:** define what a future Dyna report must answer before it can turn company claims into durable conclusions. Source IDs refer to the Dyna source registry.

## Company thesis

Dyna's current thesis has two layers. The 2025 launch framed scarce high-fidelity robot data as the bottleneck and used narrow commercial tasks to generate production experience [DYN-S010]. The 2026 research reframes pre-training around effectively unbounded human video while the deployment retrospective says task-specific post-training and deployment infrastructure remain essential [DYN-S006, DYN-S012].

Research questions:

- Is the scarce resource robot trajectories, high-quality pseudo-actions, production failure data, customer access, or deployment engineering?
- What fraction of value comes from broad pre-training versus the task-specific post-training and recovery loop?
- Why commercial/industrial/service settings first? The likely reason is bounded workflows, observable ROI and repeated data, but customer economics and task-selection criteria remain undisclosed.
- “Physical economy” means economic activity still coupled to human hours [DYN-S008]. What measured labor hours, task costs and customer ROI support Dyna's claim that its system can decouple the two?
- Does the company pursue one general model across customers, shared backbones with task heads, or separately post-trained policies distributed under a common brand?

## Embodiment

Publicly disclosed evaluation platforms include:

- stationary bimanual platforms with two 6-DOF YAM arms and in-house parallel-jaw grippers;
- the same arms with paired WUJI-2 20-DOF dexterous hands;
- an early semi-humanoid prototype for language-following evaluation;
- DYNA-SAUR, presented on the current site as mobile, dexterous and equipped with vision, tactile sensing and proprioception [DYN-S001, DYN-S006].

The 14-task Dyna-2 evaluation allocates 11 tasks to parallel jaws, two to dexterous hands and one to the semi-humanoid. The 39-task offline suite uses two stationary bimanual YAM platforms [DYN-S006]. This proves evaluation diversity within a narrow family, not arbitrary cross-embodiment transfer.

Open questions:

- Which platforms contributed robot data to post-training, and are embodiment-specific adapters or action normalizers used?
- Is the semi-humanoid only an evaluation prototype or a deployment product?
- How does DYNA-SAUR's mobile base, tactile sensing and low-level System0 controller connect to Dyna-2's disclosed action representation?
- Are tasks retargeted through end-effector space, joint space or a learned embodiment token?
- What payload, reach, cycle life, ingress protection, force/torque limits and maintenance intervals apply?
- How much of “cross-embodiment” is visual transfer versus action-space compatibility between related YAM-based systems?

## System Architecture

Dyna's current homepage presents a four-layer system diagram [DYN-S001]:

| Layer | Public label | Claimed role | Public evidence boundary |
|---|---|---|---|
| System 2 | DYNA-VLM | High-level agentic reasoning | Label and role are public; architecture, planning horizon, interfaces and evaluation are not. |
| System 1 | DYNA-2 | Mid-level task dexterity | Dyna-2 has a detailed technical report, but the report does not disclose the complete production integration. |
| System 0 | DYNA-System0 | Low-level whole-body control | Only the role label is public; control rate, training method, safety envelope and interfaces remain unknown. |
| Embodiment & sensing | DYNA-SAUR | Vision, tactile and proprioception | The platform is named, but complete hardware, calibration, force-limit and production specifications are not public. |

This layered view changes attribution. Dyna-2 should receive credit for results explicitly evaluated as a Dyna-2 policy under the technical report's protocol. It should not automatically receive credit for language decomposition, navigation, whole-body stabilization, state-machine sequencing, sensing, safety interlocks or recovery implemented elsewhere in the stack. A long-horizon workflow shown by the integrated robot may depend on DYNA-VLM, DYNA-System0, DYNA-SAUR hardware, conventional planners and undisclosed orchestration in addition to the WAM.

Open questions:

- What observations, goals and action abstractions cross the DYNA-VLM → DYNA-2 boundary?
- Does DYNA-VLM replan after failure, or does Dyna-2 own recovery only within a skill?
- What command rate and action space connect Dyna-2 to DYNA-System0?
- Which navigation, state-machine, safety and task-monitoring modules sit outside the four named components?
- Which current commercial workflows actually use this exact stack rather than earlier DYNA-1 systems?
- What fraction of long-horizon success disappears when each module is ablated?

## Data

### What is known

Dyna says most of its 1M+ hours are head-mounted first-person recordings of everyday manipulation, collected through data partners and internal operations. Episodes passing an internal quality threshold receive 3D hand-pose tracks. Wrist pose becomes an end-effector trajectory and thumb-index aperture becomes a continuous grasp signal [DYN-S006]. The scaling subsets preserve source proportions and add data rather than swap distributions.

### What remains unknown

- Who are the data partners, what are the licenses, countries, worker populations and consent rules?
- What fraction of 1M hours passes the hand-pose bar and becomes action-labeled?
- How are camera calibration, head motion, occlusion, left/right hands, object contact and handedness handled?
- Is pseudo-action expressed in camera, head, world or canonical hand coordinates?
- How are grasp apertures mapped to parallel jaws and 20-DOF hands?
- How much duplication exists across workers, tasks, clips and instructions?
- What is the error distribution of the hand-pose pipeline and how does it change across skin tone, gloves, lighting and occlusion?
- Are human-video clips temporally segmented around tasks or sampled from long streams?
- What robot data is used for task-specific post-training, how is it collected, and how many failed trajectories are retained?

### Pre-training versus post-training

In the disclosed scaling-law pre-training, no robot data is used. Human clips with hand annotations train pseudo-actions; additional unlabeled human clips train future-video prediction. The reported physical policies are then post-trained on task-specific robot demonstrations—at most 10 hours per task. For bottle-cap untwisting, the technical report says roughly 10 minutes while the corrected press release says 13 minutes [DYN-S006, DYN-S033]. Thus Dyna has not shown that human video eliminates robot data. It shows that the initial human-video checkpoint changes the outcome of matched robot post-training.

The embodiment gap is therefore reduced under the company's metrics, not “solved.” Offline robot prediction improves without robot pre-training data, but physical execution still uses robot demonstrations and related embodiments.

## World-Action Model (WAM)

### Distinguishing WAM from VLA

A conventional VLA maps visual/language context to actions and commonly inherits a vision-language backbone. Dyna-2 adds a future-video generative objective and modality-specific diffusion-transformer streams. Video and action tokens can exchange information through attention; the scaling-law variant fits separate marginal velocity fields over future video and future action [DYN-S006].

The central hypothesis is representational: predicting how the scene evolves under manipulation forces the shared trunk to encode contact, object motion and temporal structure that transfer from human hands to robot arms. Dyna's ablation reports that joint video/action training beats action-only on all 39 offline tasks, and video-only data improves offline robot metrics when action-labeled hours are fixed.

### Do not confuse two meanings of “world model”

1. **World-model training objective:** predict future video during training so the shared network learns temporal/physical features.
2. **Planning by imagined video:** generate candidate futures during inference, evaluate them and select actions.

Dyna explicitly says the disclosed scaling-law policy remains reactive: its action head neither generates nor attends to predicted future video at inference [DYN-S006]. Therefore “it imagines the future and then acts” is marketing shorthand, not the scientific mechanism described for that model variant. Dyna separately demonstrates a distilled one-step video generator for planning/evaluation use, but does not show that the reported robot policy requires it.

Open questions:

- Which production Dyna-2 variants differ from the scaling-law variant?
- Does text influence action only through video features, and what failure modes follow from this indirect path?
- What are context length, action horizon, control frequency, image resolution and inference latency?
- Does the action head predict end-effector deltas, absolute poses, gripper state or joint commands?
- What does the video objective contribute after controlling for extra compute and tokens?
- Can a frozen world-model representation support new embodiments with linear or small adapters?

## Scaling law

The main controlled independent variable is **human-video hours** in nested 1k, 10k, 100k and 1M subsets. Model size and compute scaling were explicitly left for future work. The company technical report also separates action-labeled human hours from video-only hours in two ablation regimes [DYN-S006].

| Candidate axis | Studied? | Evidence boundary |
|---|---:|---|
| Total human-video hours | Yes | Four main ladder points; company-run experiments |
| Action-labeled human hours | Yes | 5k/50k/100k objective ablation; 50k and 250k fixed-action regimes |
| Video-only human hours | Yes | Offline held-out robot prediction only |
| Robot post-training hours | Partly | Per-task maxima and one example described as roughly 10 minutes in the technical report and 13 minutes in the press release; not a systematic scaling curve |
| Model size | No | Explicitly deferred |
| Training compute | No | Intended fixed configuration; exact compute undisclosed |
| Robot embodiment diversity | Partly | Three related evaluation embodiments; not a scaling axis |

“Human-to-robot transfer scaling law” specifically means monotonically improving **offline action-prediction metrics on held-out robot trajectories** when only human pre-training hours grow. This is stronger than measuring held-out human loss, but weaker than a law for autonomous robot task success. The 14-task post-training suite adds a physical correlation, yet it uses task-specific robot data, small trial counts and heterogeneous normalized metrics.

Questions for review:

- Are four points sufficient to claim a power law, and are slopes/exponents with uncertainty available?
- Were random seeds varied, or are checkpoint windows standing in for seed uncertainty?
- Are hours the right denominator when clip length, task diversity, annotations and source quality differ?
- How sensitive is monotonicity to task weighting and accuracy thresholds?
- Could semantic overlap between human pre-training and robot evaluation create leakage without exact trajectory overlap?
- Does the 10k→100k inflection reproduce under new robot datasets or new embodiments?
- Do gains persist when post-training data, compute and hyperparameter tuning are independently controlled?

## Post-training

Dyna reports robot-only post-training without human-robot alignment or co-training in the 14-task study. Each task receives at most 10 hours of robot data; example rollouts are described as using a few hours [DYN-S006].

Unresolved details:

- Is post-training full-model fine-tuning, adapter tuning, policy-head tuning, RL, behavior cloning or a mixture?
- Is every policy task-specific, or can one post-trained checkpoint perform multiple tasks?
- Are checkpoints embodiment-specific?
- How are successful, failed and corrective trajectories weighted?
- Does a reward model participate in Dyna-2 post-training as it did in DYNA-1?
- How much engineer time, teleoperator time, station setup and hyperparameter search sits behind “few hours”?
- Does the bottle-cap difference—roughly 10 minutes in the technical report versus 13 minutes in the corrected press release—reflect rounding, accepted data, or total recorded collection time?
- What is the marginal value of more robot data at each human pre-training rung?

The true new-task adaptation cost must report at least robot recording time, attempts, human labor, calibration, environment engineering, training compute, validation trials and on-site debugging. Public sources currently provide only fragments.

## Infrastructure

Robot episodes combine multiple camera streams, proprioception and actions sampled at different rates. A training sample must align them at one timestamp, decode sparse visual frames, and often read a longer dense state/action chunk. Unlike a language corpus, the read pattern depends on modality and policy horizon. Unlike image training, inter-frame video compression makes random temporal seeking dependent on GOP boundaries.

Key terms:

- **Multi-camera streams:** simultaneous views used to reduce occlusion and provide context.
- **Proprioception:** the robot's measured internal state—joint positions, velocities and related signals.
- **Actions/action chunks:** commands over one or several future control steps.
- **Different sampling rates:** cameras, joints and actions do not naturally share the same clock frequency.
- **Video GOP:** a keyframe plus dependent frames; longer GOPs compress better but make random seeks costlier.
- **Episode container:** one inspectable file or logical unit holding synchronized topics.
- **Dataset manifest:** the exact episode ranges selected for a training run.
- **Random temporal reads:** sampling arbitrary windows rather than scanning episodes from start to finish.
- **Data curation:** selecting runs by task, success, sensor quality, schema and other criteria.
- **Cluster-local cache:** placing the active working set on NVMe near GPUs.
- **Optimizer sharding:** distributing optimizer state/update work across ranks.
- **Job resilience:** preventing degraded nodes from starting work and resuming after failures.

At one million hours, Dyna reports 43M episodes during the manifest bottleneck and later more than 50M rows. The system must process petabytes, choose experiment subsets, deliver random windows for weeks and survive node failures. More GPUs cannot repair a storage layout that requires too many round trips, an overloaded scheduler database, a 48-hour manifest crawl or remote-object-store latency.

See [`dyna_2_infrastructure_deep_dive_v1.md`](./dyna_2_infrastructure_deep_dive_v1.md) for concept-by-concept analysis.

## Deployment

| Area | Public evidence | Classification | Known metrics | Critical unknowns |
|---|---|---|---|---|
| Napkin folding | DYNA-1 internal 24h run; company says paying customers | Internal evaluation + claimed production | 850+; 99.4%; zero interventions | Full logs; customer runtime; material labor; maintenance |
| Laundry | Named Monster Laundry case | Customer deployment; interested testimonial | 200k+ items/3 months; 99% acceptance; +25% capacity | Active hours; downtime; rejects; service; economics |
| Food/hospitality | Homepage workflows; cup-filling demos | Marketing/demo; customer sites not identified | 180 items/hr; <90 sec/tub | Protocol; site; attempts; intervention; quality |
| Factory/assembly | Homepage workflow | Marketing claim | 200 units/hr; ±2 mm | Customer; task distribution; inspection; uptime |

Classification must remain explicit: a selected demo is not a pilot; a pilot is not a paid recurring production deployment; a named customer testimonial is not an independent operational audit.

Every future deployment record should require: throughput, quality rubric, success denominator, runtime window, human intervention, recovery events, adaptation hours, maintenance, robot count, editing/cuts, customer-site status and third-party observation. Unknown values remain **Unknown**, not inferred from “production-grade.”

## Reliability and safety

Public evidence for recovery includes selected DYNA-1 error-recovery clips, DYNA-1i conference disturbances and Dyna-2 perturbations such as lighting changes, a covered camera and repeated task undoing [DYN-S003, DYN-S004, DYN-S006]. The 24-hour internal run and 10-month daily-use statement provide runtime anchors. They do not establish fleet-wide 24/7 autonomy.

No adequate public disclosure was found for:

- sensor drift calibration and detection;
- hardware wear, gripper replacement or mean time between failure;
- failed-grasp rates and recovery latency distributions;
- network outage behavior and offline fallback;
- compute failure handling on deployed robots;
- safe behavior around workers and bystanders;
- emergency-stop architecture and stop-category testing;
- force, speed, torque or workspace limits;
- formal hazard analysis, applicable safety standards or certification;
- incident reporting and remote-operator intervention policy.

“Production-grade” is therefore a performance positioning statement, not evidence of complete functional safety.

## Business

### Confirmed baseline

- Founders: Lindon Gao, York Yang and Yecheng Jason Ma [DYN-S011].
- Gao and Yang previously built Caper AI; Dyna's release states a $350M exit. Ma's profile documents research in robot foundation models, RL, reward learning and sim-to-real, with prior time at Google DeepMind, NVIDIA and Meta [DYN-S016].
- Seed: $23.5M announced 2025-03-25, co-led by CRV and First Round [DYN-S010].
- Series A: $120M announced 2025-09-15. Bloomberg reported a valuation above $600M and corroborated lead investors CRV, First Round and RoboStrategy, with Nvidia, Amazon and Salesforce venture arms participating [DYN-S014].
- Commercial model: at least one customer case describes Robots-as-a-Service [DYN-S013].

### Still unknown

- Exact founding/legal incorporation date beyond the company's one-year statement in September 2025.
- Employee count at the 2026-08-18 cutoff from a reliable dated source.
- Number of paying customers, robots deployed, annualized revenue, pricing, gross margin and hardware cost.
- Who owns installed hardware, data and maintenance risk under RaaS.
- Customer concentration and renewal/retention.
- Whether hardware economics improve with DYNA-SAUR or require continuing subsidies.

No unsupported employee estimate should be added. Financing and valuation should always carry dates and distinguish company announcements from independent reporting.
