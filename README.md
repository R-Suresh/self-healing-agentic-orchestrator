# Self-Healing Agentic Orchestrator

Artifacts for the paper:

**Self-Healing Agentic Orchestrators for Reliable Tool-Augmented Large Language Model Systems**

This repository contains the controlled benchmark, experiment notebook, result files, generated figures, LaTeX tables, and representative recovery traces used in the paper.

## Overview

This project studies reliability in tool-augmented large language model systems as an orchestration-level control problem.

The paper evaluates a self-healing agentic orchestrator that implements a structured:

```text
monitor → detect → diagnose → recover → verify
```

control loop for handling orchestration-level failures such as runtime tool failures, malformed outputs, stale context, contradictory evidence, and semantic silent failures.

The experiments use synthetic benchmark tasks, simulated tools, deterministic fault injection, and fixed random seeds to isolate orchestration behavior under controlled conditions.

## Repository Structure

```text
.
├── notebooks/
│   └── self_healing_orchestrator_experiments_primary_regime_validated.ipynb
│
├── data/
│   └── tasks_100.json
│
├── results/
│   ├── results_100_tasks.csv
│   ├── overall_results_100_tasks.csv
│   ├── fault_intensity_results_100_tasks.csv
│   ├── primary_results_100_tasks.csv
│   ├── stress_results_100_tasks.csv
│   ├── failure_type_results.csv
│   ├── ablation_results.csv
│   └── semantic_results.csv
│
├── tables/
│   ├── overall_results_table.tex
│   ├── primary_stress_results_table.tex
│   ├── failure_type_results_table.tex
│   ├── ablation_results_table.tex
│   ├── semantic_silent_failure_table.tex
│   └── trace_case_studies_table.tex
│
├── figures/
│   ├── success_rate_vs_fault_intensity.png
│   ├── failure_type_success_heatmap.png
│   ├── cost_reliability_tradeoff.png
│   ├── ablation_success_rate_vs_fault_intensity.png
│   └── semantic_silent_failure_rate_vs_intensity.png
│
├── traces/
│   ├── workflow_001.json
│   ├── retrieval_001.json
│   └── retrieval_002.json
│
├── REPRODUCIBILITY.md
├── requirements.txt
├── LICENSE
└── README.md
```

Some artifact folders may also contain `MANIFEST` files. These are included only for traceability and summarize file origins, row counts, or representative trace metadata.

## Running the Experiments

The main experiment notebook is located at:

```text
notebooks/self_healing_orchestrator_experiments_primary_regime_validated.ipynb
```

To run the notebook:

1. Clone this repository.
2. Install dependencies.
3. Open the notebook in Google Colab or Jupyter.
4. Run all cells from top to bottom.

Install dependencies with:

```bash
pip install -r requirements.txt
```

The notebook generates:

- synthetic benchmark tasks
- main reliability experiment results
- failure-class analysis results
- ablation study results
- semantic silent-failure experiment results
- representative recovery traces
- generated paper figures
- LaTeX tables used in the paper

## Experimental Design

The benchmark contains 100 synthetic tool-augmented tasks across five categories:

| Category | Task Count |
|---|---:|
| Retrieval and evidence synthesis | 20 |
| Multi-step API workflows | 20 |
| Calculation and verification | 20 |
| Planning and tool selection | 20 |
| Contradiction resolution | 20 |

The main experiment compares five orchestration strategies:

1. Static workflow
2. Retry-only
3. ReAct-style
4. Full replanning
5. Self-healing orchestration

The ReAct-style and full-replanning baselines are controlled benchmark implementations. They are used to compare orchestration behavior under identical task definitions, simulated tools, fault schedules, and success criteria.

## Fault Intensity Regimes

Runtime fault intensities are split into primary and stress-test regimes:

| Regime | Fault Intensities |
|---|---|
| Primary evaluation regime | 0.0, 0.1, 0.2, 0.3 |
| Stress-test regime | 0.4, 0.5 |

The paper-facing success-rate figure uses only the primary evaluation regime. Stress-test results are preserved separately in the result files and tables.

Relevant files:

```text
results/primary_results_100_tasks.csv
results/stress_results_100_tasks.csv
tables/primary_stress_results_table.tex
figures/success_rate_vs_fault_intensity.png
```

## Expected Output Counts

A successful full run should produce:

| Output | Expected Count |
|---|---:|
| Main experiment rows | 9000 |
| Ablation experiment rows | 9000 |
| Semantic experiment rows | 7200 |
| Benchmark tasks | 100 |
| Representative trace case studies | 3 |

These counts follow from the experiment design:

- 100 benchmark tasks
- 5 orchestration methods
- 6 runtime fault intensities
- 3 deterministic seeds
- 6 self-healing ablation variants
- 5 nonzero runtime fault intensities for ablation
- 6 semantic-experiment variants
- 4 semantic fault intensities
- 3 deterministic seeds

## Key Reported Results

The generated outputs reproduce the following headline values from the paper:

| Result | Value |
|---|---:|
| Self-healing overall success | 98.8% |
| Self-healing primary-regime success | 99.6% |
| Self-healing stress-regime success | 97.3% |
| Self-healing with verifier semantic success | 100.0% |
| Self-healing with verifier semantic silent failure | 0.0% |

These results should be interpreted within the controlled benchmark setting.

## Representative Recovery Traces

The `traces/` folder contains three representative recovery traces used for trace-based diagnosability analysis:

| File | Case Study | Task ID | Fault |
|---|---|---|---|
| `workflow_001.json` | Runtime/tool failure | `workflow_001` | timeout |
| `retrieval_001.json` | Context/evidence failure | `retrieval_001` | stale context |
| `retrieval_002.json` | Semantic silent failure | `retrieval_002` | semantic wrong output |

These traces record the task, injected fault, recovery behavior, verifier activity, and final outcome.

## Artifact Scope and Limitations

This repository is a controlled research artifact.

It does **not** measure live production LLM-agent behavior, real external API behavior, or commercial model performance.

The experiments use:

- synthetic benchmark tasks
- simulated tools
- deterministic fault injection
- fixed random seeds
- controlled orchestration baselines
- task-specific success criteria
- controlled semantic wrong-output faults

This design is intentional. It allows the experiments to isolate orchestration-level reliability behavior without confounding from live API availability, model-version changes, nondeterministic LLM outputs, external service instability, or private data.

The results should therefore be interpreted as controlled evidence about orchestration behavior under the evaluated fault models, not as a universal guarantee of reliability in open-ended deployments.

## Reproducibility Notes

Detailed reproducibility instructions are available in:

```text
REPRODUCIBILITY.md
```

That file describes:

- artifact scope
- repository structure
- environment setup
- expected output counts
- primary vs stress-test regimes
- benchmark task schema
- experiment descriptions
- expected headline results
- interpretation limits

## Release

The arXiv v1 artifact release is available at:

```text
https://github.com/R-Suresh/self-healing-agentic-orchestrator/releases/tag/v1.0-arxiv
```

## License

This repository is released under the MIT License.
