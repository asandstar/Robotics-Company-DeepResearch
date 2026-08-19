# Dyna-2 Infrastructure Deep Dive v1

**Source under review:** Dyna Robotics, “Training Dyna-2 at million-hour scale, repeatably,” 2026-08-17 [DYN-S007].  
**Audience:** robot-learning researchers who know training pipelines but not large-scale data infrastructure.  
**Evidence boundary:** all Dyna performance numbers are first-party internal measurements. The cited software documentation explains components but does not independently reproduce Dyna's results.

## Technical summary

Dyna's strongest infrastructure contribution is not one isolated optimization. It is a sequence of bottleneck removals across the entire lifecycle:

```text
collection → episode storage → ingestion/QA/enrichment → warehouse curation
→ training manifest → cluster-local delivery → distributed optimizer → restart/recovery
```

At one million hours, the dominant cost moves. Per-frame images make storage and reads expensive; a monolithic ingestion job overloads the scheduler; millions of file lookups make each experiment wait days; remote object storage starves GPUs; global optimizer sharding saturates inter-node links; multi-week jobs inevitably encounter degraded nodes. Buying more GPUs does not remove any of those walls.

The company article reports ~68% lower storage, ~2.9× faster sample reads, ingestion growth from 14k to 440k episode-hours/week, startup from ~48 hours to under one minute, ~2 GB/s cached reads per node, 98% warm-run GPU utilization and an optimizer step ~3× faster at the relevant scale [DYN-S007]. These are internal, workload-specific engineering measurements, not universal benchmarks or independent reproductions.

## Why robot data differs from ordinary ML data

An LLM sample is usually a slice of one token stream; an image sample is often one independently decodable object. A robot episode contains cameras, robot state and actions with different clocks. A policy may need a few decoded frames but a long dense action/state window. The storage system must therefore support synchronized random temporal access while preserving inspectability and compression. World-action training further changes the read pattern: longer contiguous video sequences amortize keyframe decode cost better than sparse-frame VLA sampling.

## Storage and sampling concepts

### MCAP

- **What it is:** a container format for heterogeneous timestamped data, widely used for robotics/autonomy logs [DYN-S024].
- **Why Dyna needs it:** cameras, proprioception and actions can live in one indexed, inspectable episode with flexible chunks.
- **Original bottleneck:** HDF5 files holding independent JPEG frames were large and required conversion for inspection.
- **Reported improvement:** after Dyna-specific encoding, codec and chunk-layout changes, episodes became smaller and required fewer reads [DYN-S007]. The gain is not attributable to MCAP alone.
- **Problem type / lesson:** MCAP is robotics-oriented, but tuning remains workload-specific. Teams should select the container only after defining sample windows and topic access patterns.

### HDF5

- **What it is:** a self-describing hierarchical file format for arrays and metadata [DYN-S025].
- **Why it appeared:** HDF5 is convenient for research arrays and supported Dyna's earlier per-frame JPEG representation.
- **Original bottleneck:** the chosen representation did not exploit inter-frame compression and had no native episode viewer in Dyna's workflow.
- **Reported improvement after replacement:** storage and sample-read gains came from the new encoding/chunking design, not from a universal claim that HDF5 is slow.
- **Problem type / lesson:** general scientific storage. Do not infer “HDF5 versus MCAP” performance without matching codecs, indexing and access patterns.

### H.264

- **What it is:** an inter-frame video coding standard that compresses frames using temporal redundancy [DYN-S026].
- **Why Dyna needs it:** independent JPEGs repeat scene content and cost roughly hundreds of MB per camera-minute under the article's example conditions.
- **Original bottleneck:** storage and streaming volume grew with every camera.
- **Reported improvement:** the combined tuned MCAP/H.264 and layout redesign produced Dyna's reported ~68% storage reduction versus its per-frame JPEG baseline [DYN-S007]. The article does not isolate H.264's share.
- **Problem type / lesson:** general video engineering, made robotics-specific by synchronized random window sampling and multi-camera episodes.

### GOP (group of pictures)

- **What it is:** a keyframe followed by dependent frames; decoding an arbitrary frame starts at the preceding keyframe.
- **Why Dyna needs it:** GOP length trades compression against seek/decode work.
- **Original bottleneck:** sparse VLA samples repeatedly pay seek cost, while a WAM reads longer contiguous sequences.
- **Reported improvement:** Dyna used larger GOPs for the WAM read pattern, amortizing a keyframe over more requested frames.
- **Problem type / lesson:** general video tradeoff. Policy context length is effectively a storage hyperparameter; benchmark GOPs against the actual sampler.

### Random access

- **What it is:** reading a time window at an arbitrary offset instead of scanning an episode sequentially.
- **Why Dyna needs it:** shuffled training draws windows across tens of millions of episodes.
- **Original bottleneck:** each topic or GOP can create extra seeks and object-store round trips.
- **Reported improvement:** indexing plus topic groups reduced fetch count and raised sample speed.
- **Problem type / lesson:** general data-system issue with unusual severity in robotics because one logical sample spans multiple clocks and modalities.

### Topic-group chunking

- **What it is:** writing topics with similar access patterns together in time-major chunks—cameras together, state/actions together—instead of one chunk stream per topic.
- **Why Dyna needs it:** a sample needs multiple cameras but dense state/action windows of a different length.
- **Original bottleneck:** default per-topic chunks made each additional sensor add another fetch.
- **Reported improvement:** Dyna reports ~3.4× fewer chunk fetches and ~2.9× faster reads after the topic-group chunk-layout redesign under its workload [DYN-S007].
- **Problem type / lesson:** robotics-specific schema design. Group by how training reads data, not only by sensor identity.

### Proprioception

- **What it is:** the robot's internal measured state, typically joint positions, velocities and related signals.
- **Why Dyna needs it:** the action policy conditions on current body state and aligns commands with images.
- **Original bottleneck:** dense state samples have different frequency and window length from video.
- **Reported improvement:** separating cameras from state/actions prevents sparse video access from forcing the dense state stream into the same chunks.
- **Problem type / lesson:** robotics-specific modality; synchronization and calibration must be versioned as carefully as images.

### Action chunk

- **What it is:** a sequence of future commands predicted/consumed as one training target.
- **Why Dyna needs it:** chunked policies model temporally coherent control rather than isolated actions.
- **Original bottleneck:** each sample may require a much longer action/proprioception sequence than its visual context.
- **Reported improvement:** modality-specific chunks make one dense range read sufficient.
- **Problem type / lesson:** robotics-specific. Changing horizon or control frequency changes storage I/O and cache behavior, not only the model.

## Ingestion and orchestration concepts

### Data ingestion

- **What it is:** converting raw uploads into canonical, validated, enriched training episodes.
- **Why Dyna needs it:** vendors and robots produce different formats, clocks, schemas and quality failures.
- **Original bottleneck:** the pipeline plateaued at 14k episode-hours/week; 1M hours would take roughly 16 months by Dyna's arithmetic.
- **Reported improvement:** after decomposing stages and jointly redesigning batching, scheduling and pipeline execution, Dyna reports throughput reaching 440k episode-hours/week [DYN-S007]. This is not an Airflow-only speedup.
- **Problem type / lesson:** general data engineering plus robotics-specific synchronization, sensor QA and action semantics.

### DAG

- **What it is:** a directed acyclic graph of tasks and dependencies [DYN-S027].
- **Why Dyna needs it:** transformation, black-frame checks, pose validation, captioning and segmentation need different resources and retry semantics.
- **Original bottleneck:** one all-or-nothing job allocated for the worst step and made partial failure expensive.
- **Reported improvement:** steps scale independently, noncritical failures no longer cancel all work, and partial reprocessing becomes explicit.
- **Problem type / lesson:** general workflow orchestration. Robotics teams benefit when schema, robot software and pipeline versions are stamped into every episode.

### Airflow

- **What it is:** a workflow orchestrator that schedules DAG tasks and records state [DYN-S027].
- **Why Dyna needs it:** millions of episodes and vendor-specific branches require retries, observability and resumable multi-day runs.
- **Original bottleneck:** a single Kubernetes job coupled every stage and kept state in a live process.
- **Reported improvement:** durable step state, replay, concurrent runs and runtime-selectable profiles.
- **Problem type / lesson:** general infrastructure. Airflow itself did not create 31× throughput; Dyna also redesigned batching and scheduler load.

### Kubernetes

- **What it is:** a container workload orchestrator [DYN-S028].
- **Why Dyna used it:** run isolated processing containers with allocated resources.
- **Original bottleneck:** Dyna's earlier design put the entire pipeline in one Kubernetes job; this is an application architecture limitation, not necessarily a Kubernetes limitation.
- **Reported improvement after redesign:** Airflow controlled finer-grained workers and stage-specific resources.
- **Problem type / lesson:** general infrastructure. Separate orchestration semantics from the execution substrate.

### Staggered starts

- **What it is:** deliberately offsetting similar batches so they do not finish and write state simultaneously.
- **Why Dyna needs it:** millions of concurrent runs can create synchronized bursts against the scheduler database.
- **Original bottleneck:** lockstep completions flooded metadata writes and stalled scheduling.
- **Reported improvement:** smoother write load helped unlock horizontal ingestion scaling.
- **Problem type / lesson:** general distributed-systems backpressure; measure the control plane, not only worker utilization.

### Bin packing

- **What it is:** assigning variably sized files to batches with near-equal total bytes.
- **Why Dyna needs it:** robot episodes vary widely in length and number of streams.
- **Original bottleneck:** equal episode counts create unequal byte workloads, long-tail stragglers and idle workers.
- **Reported improvement:** byte-balanced chunks better respect network, disk, database and node limits.
- **Problem type / lesson:** general scheduling technique with robotics-specific file-size variance. Pack by measured cost proxy, not row count.

## Curation and manifest concepts

### Data warehouse

- **What it is:** an analytical system optimized for scanning selected columns over large tables.
- **Why Dyna needs it:** each experiment filters episodes by task, robot, success, sensor quality and software version.
- **Original bottleneck:** a transactional database served live operations, while file crawls made four storage trips per episode.
- **Reported improvement:** Dyna says curation became one SQL query over an episode table exceeding 50M rows [DYN-S007].
- **Problem type / lesson:** general analytical architecture. Keep operational writes and experiment-wide scans on systems optimized for each workload.

### Transactional database

- **What it is:** a system of record optimized for correct, concurrent row-level updates.
- **Why Dyna needs it:** collection and annotation operations require integrity and current state.
- **Original bottleneck:** forcing it to serve full-corpus analytical scans conflicted with operational traffic.
- **Reported improvement:** the production DB remained authoritative while a warehouse served curation.
- **Problem type / lesson:** general data architecture. Do not make the operational control plane double as the training analytics engine.

### Change-data-capture (CDC)

- **What it is:** propagating inserts and updates from the transactional source into an analytical copy.
- **Why Dyna needs it:** the warehouse must remain nearly current without scanning/rebuilding everything.
- **Original bottleneck:** duplicated metadata can become stale or require expensive batch synchronization.
- **Reported improvement:** Dyna says the warehouse trails the source by seconds, acceptable because training uses processed episodes.
- **Problem type / lesson:** general infrastructure. Define freshness tolerance and lineage; perfect real-time consistency is often unnecessary for training.

### Manifest

- **What it is:** the exact list of episode ranges and locations selected for a run.
- **Why Dyna needs it:** batching, sharding, epoch size and reproducibility depend on a frozen dataset definition.
- **Original bottleneck:** Dyna reports that at 43M episodes, building the manifest from paths and sidecars took about 48 hours [DYN-S007].
- **Reported improvement:** Dyna reports that a warehouse query plus materialized columnar, memory-mapped manifest reduced the broader cold-start path from about 48 hours to under one minute at the 43M-episode stage [DYN-S007].
- **Problem type / lesson:** common in large ML, unusually central when robot episodes are filtered by sensor validity and software versions.

### Memory mapping

- **What it is:** mapping a file into virtual memory so pages load on demand rather than materializing the entire table.
- **Why Dyna needs it:** loading a 1M-hour manifest independently on every rank exceeded roughly 2 TB node RAM.
- **Original bottleneck:** repeated network reads were slow and fully materialized copies exhausted memory.
- **Reported improvement:** one local download, per-rank zero-copy slices and on-demand pages reduced loading from minutes to seconds.
- **Problem type / lesson:** general systems technique. “Shard on load” only saves memory if the full table is not first read into RAM.

## Data delivery concepts

### NVMe

- **What it is:** high-throughput local solid-state storage attached to each GPU node.
- **Why Dyna needs it:** GPU clusters may be far from the canonical cloud bucket, and copying the entire petabyte corpus to each vendor is impractical.
- **Original bottleneck:** remote reads suffered latency, packet loss and vendor-specific mounts.
- **Reported improvement:** local pages served the active working set predictably and reused storage already included with nodes.
- **Problem type / lesson:** general locality optimization. Cache capacity planning must follow working-set size and eviction behavior.

### Cluster-local cache

- **What it is:** a distributed cache over node-local NVMe, with misses falling back to object storage; Dyna uses Alluxio [DYN-S029].
- **Why Dyna needs it:** multi-week runs need stable throughput across multiple cloud/GPU vendors.
- **Original bottleneck:** a single remote reader sustained ~200 MB/s in Dyna's setup and exposed GPUs to stalls.
- **Reported improvement:** Dyna reports ~2 GB/s per node and 98% GPU utilization for a warm multi-node run using the cluster-local cache design [DYN-S007]. Node count, utilization definition and cold-cache behavior are not disclosed.
- **Problem type / lesson:** general ML infrastructure, amplified by large multi-camera files. Pre-warm the manifest's working set and report cold as well as warm results.

## Distributed training concepts

### Optimizer sharding

- **What it is:** partitioning optimizer state/update work across distributed ranks, related to FSDP/ZeRO patterns [DYN-S030].
- **Why Dyna needs it:** optimizer work reportedly consumed about half the step time.
- **Original bottleneck:** sharding across all nodes caused broadcast traffic over slower InfiniBand links.
- **Reported improvement:** sharding within a node and duplicating work across nodes removed most cross-node optimizer traffic.
- **Problem type / lesson:** general distributed training. The correct balance between redundant compute and communication changes with node count.

### Topology-aware communication

- **What it is:** choosing a collective/sharding pattern based on fast intra-node NVLink versus slower inter-node fabric.
- **Why Dyna needs it:** B200 intra-node bandwidth was stated as roughly 1.8 TB/s per GPU, more than an order above per-GPU inter-node links.
- **Original bottleneck:** global sharding scaled communication with node count and produced long-tail steps.
- **Reported improvement:** Dyna reports the optimizer-step component averaged ~3× faster at the disclosed switching scale; this is not a 3× end-to-end training speedup [DYN-S007]. Dyna retains global sharding for smaller jobs.
- **Problem type / lesson:** general large-model training. Report end-to-end step gain separately from component microbenchmarks.

## Reliability concepts

### Preflight gating

- **What it is:** health checks before scheduling work onto a node.
- **Why Dyna needs it:** a synchronous run stalls when any rank has a degraded GPU, bad disk, kernel issue or broken container runtime.
- **Original bottleneck:** superficially healthy nodes caused repeated crashes; lifetime error counters could also falsely condemn recovered nodes.
- **Reported improvement:** Dyna says its Slurm-based preflight layer drains nodes using reboot-reset counters and checks GPU inventory, logs, local disk and runtime [DYN-S007]; the Slurm documentation only establishes the scheduler context [DYN-S031].
- **Problem type / lesson:** general cluster reliability. Gate on predictive, resettable signals and measure false positives/negatives.

### Checkpoint recovery / job resilience

- **What it is:** saving model/optimizer state and automatically requeuing a failed job from the latest durable checkpoint.
- **Why Dyna needs it:** multi-week runs almost guarantee node failure, while every lost interval wastes the whole fleet's work.
- **Original bottleneck:** dead workers, drained nodes and stale reservations required manual discovery and restart.
- **Reported improvement:** Dyna describes in-place worker restarts, Slurm requeue, attempt-keyed diagnostics and periodic cleanup, with cluster provisioning codified through Ansible [DYN-S007]. Slurm and Ansible documentation explain the tools but do not verify Dyna's reliability improvement [DYN-S031, DYN-S032].
- **Problem type / lesson:** general HPC/ML. Publish checkpoint interval, lost-work distribution and mean recovery time to quantify the benefit.

## What the article establishes—and what it does not

### Reasonably supported by the company account

- Dyna encountered multiple scale-dependent bottlenecks rather than one GPU shortage.
- The final system co-designs the episode format, sampler, metadata plane, cache and trainer.
- The direction of the changes is technically coherent and broadly transferable.
- Reported before/after magnitudes are specific enough to guide questions for other teams.

### Not established publicly

- Total infrastructure cost per usable hour or per training run.
- Dataset quality yield, annotation cost and how much of 1M hours is action-labeled.
- Cold-cache end-to-end utilization across all clusters.
- Full training time, node count, GPU-hours, energy use or model size.
- Independent reproduction of any performance figure.
- Whether infrastructure gains translate into better robot task performance rather than faster iteration alone.

## Lessons for VLA/WAM teams

1. Define the sample access pattern before choosing the episode format.
2. Version raw data, canonical data, robot software, schema and transformations together.
3. Split transactional collection state from analytical dataset selection.
4. Treat the manifest as a first-class reproducibility artifact.
5. Design for heterogeneous clouds: keep a canonical corpus and move only the working set to compute.
6. Benchmark warm and cold I/O, component speed and end-to-end GPU utilization separately.
7. Expect scale to move the bottleneck; instrument every boundary instead of adding workers blindly.
8. Assume long jobs will fail. Preflight, checkpoint, restart and preserve diagnostics from the first large run.
