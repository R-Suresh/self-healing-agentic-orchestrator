# Reproducibility Notes

This repository contains the experiment artifacts for the paper:

**Self-Healing Agentic Orchestrators for Reliable Tool-Augmented Large Language Model Systems**

The artifact is intended to support inspection and reproduction of the controlled experiments reported in the paper. The experiments evaluate orchestration-level reliability for tool-augmented LLM-style agents using synthetic benchmark tasks, simulated tools, deterministic fault injection, and fixed random seeds.

## Artifact Scope

This repository is a controlled research artifact. It is **not** a live production LLM-agent system and does **not** measure the behavior of a commercial LLM, real external APIs, or production infrastructure.

The experiments are designed to isolate the behavior of orchestration strategies under matched task and fault conditions. This design makes the results reproducible and allows direct comparison across the evaluated orchestration methods.

## Repository Structure

```text
notebooks/
  self_healing_orchestrator_experiments_primary_regime_validated.ipynb

data/
  tasks_100.json

results/
  results_100_tasks.csv
  overall_results_100_tasks.csv
  fault_intensity_results_100_tasks.csv
  primary_results_100_tasks.csv
  stress_results_100_tasks.csv
  failure_type_results.csv
  ablation_results.csv
  semantic_results.csv

tables/
  overall_results_table.tex
  primary_stress_results_table.tex
  failure_type_results_table.tex
  ablation_results_table.tex
  semantic_silent_failure_table.tex
  trace_case_studies_table.tex

figures/
  success_rate_vs_fault_intensity.png
  failure_type_success_heatmap.png
  cost_reliability_tradeoff.png
  ablation_success_rate_vs_fault_intensity.png
  semantic_silent_failure_rate_vs_intensity.png

traces/
  workflow_001.json
  retrieval_001.json
  retrieval_002.json

requirements.txt
```

Some folders may also contain a `MANIFEST` file. These manifest files are included only for traceability and summarize file origins, row counts, or representative trace metadata.

## Environment

The notebook was developed for Google Colab / Jupyter using Python 3.

Install dependencies with:

```bash
pip install -r requirements.txt
```

The core dependencies are:

```text
numpy
pandas
matplotlib
tqdm
```

The notebook uses only a standard scientific Python stack and does not require access to commercial LLM APIs, private datasets, cloud credentials, or external services.

## Running the Notebook

Open the notebook:

```text
notebooks/self_healing_orchestrator_experiments_primary_regime_validated.ipynb
```

Then run all cells from top to bottom.

The notebook performs the following steps:

1. Defines the simulated data and tool environment.
2. Defines deterministic fault-injection logic.
3. Generates the 100-task benchmark.
4. Validates the benchmark under the zero-fault setting.
5. Implements the compared orchestration strategies.
6. Runs Experiment 1: main comparative reliability.
7. Runs Experiment 2: failure-class analysis.
8. Runs Experiment 3: ablation study.
9. Runs Experiment 4: semantic silent-failure experiment.
10. Runs Experiment 5: trace and diagnosability analysis.
11. Exports result CSV files, LaTeX tables, figures, and representative JSON recovery traces.

## Expected Output Counts

A successful run should produce the following high-level counts:

| Output | Expected Count |
|---|---:|
| Main experiment rows | 9000 |
| Ablation experiment rows | 9000 |
| Semantic experiment rows | 7200 |
| Representative trace case studies | 3 |
| Benchmark tasks | 100 |

These counts are derived from the experimental design:

- 100 benchmark tasks
- 5 orchestration strategies in the main experiment
- 6 runtime fault intensities
- 3 deterministic seeds
- 6 self-healing ablation variants
- 5 nonzero runtime fault intensities for ablation
- 6 semantic-experiment variants
- 4 semantic fault intensities
- 3 deterministic seeds

## Fault Intensity Regimes

Runtime fault intensities are split into primary and stress-test regimes:

| Regime | Fault Intensities |
|---|---|
| Primary evaluation regime | 0.0, 0.1, 0.2, 0.3 |
| Stress-test regime | 0.4, 0.5 |

The paper-facing success-rate figure uses the primary evaluation regime only. Stress-test results are preserved separately in the result files and tables.

Relevant files:

```text
results/primary_results_100_tasks.csv
results/stress_results_100_tasks.csv
tables/primary_stress_results_table.tex
figures/success_rate_vs_fault_intensity.png
```

## Benchmark Tasks

The benchmark contains 100 synthetic tool-augmented tasks across five categories:

| Category | Task Count |
|---|---:|
| Retrieval and evidence synthesis | 20 |
| Multi-step API workflows | 20 |
| Calculation and verification | 20 |
| Planning and tool selection | 20 |
| Contradiction resolution | 20 |

The benchmark task file is:

```text
data/tasks_100.json
```

Each task record contains:

```text
task_id
category
user_request
required_tools
expected_answer
success_criteria
eligible_faults
execution_plan
```

## Compared Orchestration Strategies

The main experiment compares five orchestration strategies:

1. Static workflow
2. Retry-only
3. ReAct-style
4. Full replanning
5. Self-healing

The ReAct-style and full-replanning strategies are controlled benchmark baselines. They are not intended to represent every possible implementation of ReAct or planner-based agents. They are included to isolate orchestration behavior under identical tasks, simulated tools, fault schedules, and success criteria.

## Experiments Included

### Experiment 1: Main Comparative Reliability

Evaluates the five orchestration strategies across all runtime fault intensities.

Primary files:

```text
results/results_100_tasks.csv
results/overall_results_100_tasks.csv
results/fault_intensity_results_100_tasks.csv
tables/overall_results_table.tex
figures/success_rate_vs_fault_intensity.png
```

### Experiment 2: Failure-Class Analysis

Analyzes performance by injected failure type over executions where at least one runtime or tool-output fault was actually injected.

Primary files:

```text
results/failure_type_results.csv
tables/failure_type_results_table.tex
figures/failure_type_success_heatmap.png
```

### Experiment 3: Ablation Study

Compares the full self-healing orchestrator against ablated variants.

Primary files:

```text
results/ablation_results.csv
tables/ablation_results_table.tex
figures/ablation_success_rate_vs_fault_intensity.png
```

### Experiment 4: Semantic Silent-Failure Experiment

Evaluates wrong-but-plausible semantic outputs that are syntactically valid but incorrect under task-specific success criteria.

Primary files:

```text
results/semantic_results.csv
tables/semantic_silent_failure_table.tex
figures/semantic_silent_failure_rate_vs_intensity.png
```

### Experiment 5: Trace and Diagnosability Analysis

Extracts representative recovery traces for qualitative inspection.

Primary files:

```text
traces/workflow_001.json
traces/retrieval_001.json
traces/retrieval_002.json
tables/trace_case_studies_table.tex
```

The representative traces correspond to:

| File | Case Study | Task ID | Fault |
|---|---|---|---|
| `workflow_001.json` | Runtime/tool failure | `workflow_001` | timeout |
| `retrieval_001.json` | Context/evidence failure | `retrieval_001` | stale context |
| `retrieval_002.json` | Semantic silent failure | `retrieval_002` | semantic wrong output |

## Key Expected Results

The artifact outputs should reproduce the following headline values reported in the paper:

| Result | Expected Value |
|---|---:|
| Self-healing overall success | 98.8% |
| Self-healing primary-regime success | 99.6% |
| Self-healing stress-regime success | 97.3% |
| Self-healing with verifier semantic success | 100.0% |
| Self-healing with verifier semantic silent failure | 0.0% |

These values are generated from the controlled benchmark and should be interpreted within that experimental scope.

## Important Interpretation Notes

The results should not be interpreted as claims about arbitrary real-world LLM-agent deployments.

In particular:

- The benchmark tasks are synthetic.
- The tools are simulated.
- Faults are injected deterministically.
- Model calls are represented as controlled planning or reasoning events.
- The verifier uses task-specific success criteria.
- The semantic silent-failure experiment uses controlled wrong-but-plausible outputs.

This controlled design is intentional. It allows the experiments to isolate orchestration-level recovery behavior without confounding from live API behavior, model-version changes, external-service instability, nondeterministic LLM outputs, or private data.

## Reproducing Figures and Tables

Running the notebook end-to-end regenerates the result CSV files, figures, LaTeX tables, and trace JSON files.

The generated paper-facing figures are located in:

```text
figures/
```

The generated LaTeX tables are located in:

```text
tables/
```

The generated raw and summarized result files are located in:

```text
results/
```

## Known Limitations

This artifact has the same scope limitations described in the paper:

- It evaluates controlled synthetic tasks rather than naturally occurring production workloads.
- It uses simulated tools rather than real external APIs.
- It uses deterministic fault injection rather than live infrastructure failures.
- It evaluates controlled orchestration strategies rather than full production agent frameworks.
- It uses task-specific verification rather than a general-purpose semantic verifier.

These limitations are intentional for the controlled reliability experiment and should be considered when interpreting the results.

## License

The repository is released under the MIT License.
