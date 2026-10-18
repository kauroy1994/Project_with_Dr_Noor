# FAMP: Diagnosis-Guided Executable Functional Memory for Continual Learning

**Research code and evolving experimental record**  
**Project status:** Research prototype / preliminary evaluation — not a validated state-of-the-art continual-learning method.

FAMP investigates whether a continual learner can **discover, validate, store, and reuse compact executable functional units** as new tasks arrive, replacing some episodic replay while maintaining competitive accuracy and improving the **accuracy–memory–compute trade-off**.

This repository accompanies a manuscript in preparation for *MLSys*. The README will be updated as implementations, independent baselines, confirmatory evaluations, and new insights become available.

## Research question

> Can counterfactually validated, shared executable functions replace part of episodic memory and yield a better continual-learning resource–accuracy trade-off than replay, compressed replay, and equally budgeted non-sharing modules?

The central hypothesis is not that every learned task can be decomposed into reusable functions. Rather, **when transferable computational behavior exists**, a carefully admitted shared unit may be cheaper to retain and reuse across tasks than repeatedly storing examples or creating independent task-specific copies.

## How FAMP works

The current prototype studies the following pipeline:

1. **Learn:** Train on incoming task data, with a bounded fallback model and any retained episodic memory.
2. **Propose:** Consider existing reusable units, compositions of units, and new compact learned units.
3. **Validate:** Evaluate candidate behavior using separated selection and acceptance data, including controlled input perturbations and teacher-relative harm checks.
4. **Diagnose / admit:** Reject proposals that do not satisfy the declared acceptance requirements; keep a fallback for those tasks.
5. **Store:** Preserve accepted units and task interfaces. Remove the associated task's ordinary replay examples when a proposal is accepted.
6. **Reuse:** Route later tasks to retained functions where they provide measurable utility, under a complete byte budget.

**Important:** An accepted unit is an executable learned component, not automatically a uniquely identified symbolic or causal abstraction. Counterfactual consistency is evidence about tested behavior, not a universal proof of causal equivalence.

## Repository contents

The repository is initially organized around an executable notebook and its preliminary report:

| File | Description |
| --- | --- |
| [`FAMP_Executable_Functional_Memory_Colab.ipynb`](./FAMP_Executable_Functional_Memory_Colab.ipynb) | Self-contained Colab-oriented experimental prototype, configurations, evaluation, and plots |
| [`FAMP_Executed_Pilot_Insights.pdf`](./FAMP_Executed_Pilot_Insights.pdf) | Preliminary measured findings, caveats, and directions for confirmatory evaluation |

Future additions may include a modular Python package, reproducible experiment configurations, tests, benchmark adapters, results tables, and manuscript assets. These are **planned**, not asserted to exist in the repository today.

## Getting started

1. Open [the notebook](./FAMP_Executable_Functional_Memory_Colab.ipynb), then choose **Open in Colab** from GitHub/Jupyter if offered, or use [Google Colab](https://colab.research.google.com/) and select **File → Open notebook → GitHub** to locate this repository.
2. Read the introductory protocol, environment, and configuration cells.
3. Run the small validation/smoke configuration first, if available in the notebook; then use the documented preliminary profile.
4. Keep the exact configuration, software versions, random seeds, and output artifacts alongside any reported metrics.
5. Consult the [pilot-insights report](./FAMP_Executed_Pilot_Insights.pdf) for interpretation of the initial results.

**Reproducibility note:** Earlier execution occurred in a controlled local CPU environment. Hosted Colab runtime, dependencies, hardware, numerical behavior, and duration may differ. Do not treat a notebook being available as proof that every future configuration or public benchmark has been executed.

## Research questions and evaluation

| RQ | Question | Required evidence |
| --- | --- | --- |
| **RQ1 — Efficiency** | Does replacing episodes with accepted units improve the accuracy–resource frontier? | Accuracy, forgetting, acquisition, persistent bytes, temporary workspace, training cost, inference overhead; matched-resource baselines |
| **RQ2 — Reuse** | Is there an actual benefit from **sharing** units across tasks? | Shared versus independent units with matched admission, training, and accounting; reuse traces and causal removal tests |
| **RQ3 — Reliability** | Does counterfactual admission avoid harmful substitutions? | Accepted/rejected proposals, teacher-relative errors, independent acceptance tests, ungated and shuffled-response controls |
| **RQ4 — Systems costs** | Which steps dominate computation and storage? | Discovery, candidate search, rejected proposals, validation, unit storage, routes, fallback, serialization, serving |
| **RQ5 — Limits** | When should the learner fall back to episodic memory? | Unrelated tasks, weak teachers, shifted inputs, small acceptance sets, long task streams and memory saturation |

**Principal success criterion:** A favorable **Pareto trade-off**, not maximum accuracy alone. Competitive accuracy with substantially lower *total* memory or compute is potentially meaningful; smaller replay buffers alone do **not** demonstrate lower total storage.

In all comparisons, account for unique unit weights, heads/adapters, routing metadata, retained witnesses, fallback model, residual replay, and training workspace. Report measured wall time and peak device memory separately from estimates of FLOPs or persistent serialized bytes.

## Preliminary findings (not final paper results)

The initial executable study supplies a small-scale feasibility test rather than an independent confirmatory benchmark.

In the reported **48 KiB arithmetic condition**, mean final accuracies were:

| Pilot condition | Mean final accuracy |
| --- | ---: |
| Packed replay | 93.55% |
| Independent compact units | 94.92% |
| FAMP (shared functional memory) | 94.97% |

The shared FAMP condition outperformed packed replay descriptively in this pilot, but its mean advantage over **independent compact units was just 0.05 percentage points**, with uncertainty that did not establish a benefit attributable specifically to sharing. Unit search and validation also incur computation.

In the task-aware Digits pilot, limited class-stratified acceptance data restricted admission under the default confidence requirements; the gated method retained fallback behavior. This is a boundary of the tested admission protocol, not evidence that no useful image functions exist.

**Do not cite these pilot measurements as final MLSys findings.** Verify the values, conditions, sample counts, uncertainty, execution environment, and limitations in the linked report before using them. Paper tables and independent benchmark results are pending.

## Experimental principles

- **No test leakage:** Task admission, hyperparameter choices, and resource-budget decisions must use the designated training/validation partitions, not final test data.
- **Matched comparisons:** Include packed replay, compressed replay, separate task-specific units, and ungated/shuffled admission controls. Explicitly state any difference in data or compute access.
- **Full-cost accounting:** Include failed candidate searches, retained model/fallback state, unit interfaces, validation witnesses, and transient training workspace.
- **Independent replication:** Report seed-level uncertainty, multiple task orders where applicable, and confirmation on benchmark data not repeatedly used for development.
- **Evidence-based claims:** Separate implemented mechanisms, constructed guarantees, pilot observations, and not-yet-tested hypotheses.
- **Failure reporting:** Record rejection rates, harmful substitutions, and cases where replay is more efficient than functional memory.

## Roadmap

- [x] Initial executable functional-memory prototype and pilot report
- [ ] Modularize and document the Python implementation and configuration files
- [ ] Add automated tests and environment/seed reproducibility manifests
- [ ] Validate admission, replay substitution, and sharing on independently held-out streams
- [ ] Implement and evaluate strong packed/compressed-replay and non-sharing baselines
- [ ] Complete standard class-incremental and longer-stream benchmark runs
- [ ] Measure GPU time, FLOPs estimates, peak allocated memory, serialization size, and inference overhead
- [ ] Populate the manuscript's RQ-linked result tables and publish reproducibility artifacts
- [ ] Freeze a versioned release and finalize citation/license details

## Paper and citation

**Working manuscript title:** *FAMP: Diagnosis-Guided Executable Memory for Byte-Budgeted Continual Learning*.

A citable public paper, DOI, authorship list, and BibTeX entry will be added once finalized. Until then, please cite the exact repository commit and any released artifact version when discussing reproducibility.

## Contributions and responsible release

Questions, reproducibility reports, and suggestions for stronger controls are welcome through GitHub Issues. As the codebase grows, contribution instructions and a code of conduct may be added.

**License:** Not yet specified in this repository. The absence of a license does not grant permission to reuse or redistribute the code; a license should be chosen before a formal public software release.

**Status disclaimer:** Research in progress. The repository should not be interpreted as establishing state-of-the-art performance, proven causal abstraction discovery, or universal memory savings.
