# Artifact Map

This document maps the paper experiments to the repository artifacts that support them.

Status: **Draft / rough mapping**  
Purpose: Help reviewers and future readers understand which notebooks, result files, figures, tables, and traces correspond to each experiment.

> Note: This map is intentionally conservative. Some model-in-the-loop output filenames may need to be updated once the final committed artifact names are confirmed.

---

## 1. Repository Artifact Overview

The repository contains artifacts for two broad evaluation tracks:

1. **Controlled benchmark experiments**  
   These use 100 synthetic tool-augmented tasks, simulated tools, deterministic fault injection, and fixed seeds to isolate orchestration behavior.

2. **Model-in-the-loop validation experiments**  
   These use a live tool-calling language model for tool selection, argument generation, and answer synthesis, while tools remain local/deterministic and faults remain controlled.

---

## 2. High-Level Experiment Map

| Experiment | Purpose | Main Notebook | Primary Result Files | Figures / Tables | Expected Rows | Paper Location |
|---|---|---|---|---|---:|---|
| Controlled main benchmark | Compare self-healing orchestration against static workflow, retry-only, ReAct-style, and full replanning baselines. | `notebooks/self_healing_orchestrator_experiments.ipynb` | `results/overall_results_100_tasks.csv`; `results/fault_intensity_results_100_tasks.csv`; `results/primary_results_100_tasks.csv`; `results/stress_results_100_tasks.csv` | `figures/success_rate_vs_fault_intensity.png`; `figures/cost_reliability_tradeoff.png`; `tables/overall_results_table.tex`; `tables/primary_stress_results_table.tex` | 9000 | Main Results |
| Failure-type analysis | Show how each method performs across injected fault classes such as timeout, unavailable tool, malformed output, schema drift, stale context, and contradictory evidence. | `notebooks/self_healing_orchestrator_experiments.ipynb` | `results/failure_type_results_100_tasks.csv`; `results/failure_type_results_100_tasks_raw.csv`; `results/failure_type_success_pivot.csv`; `results/failure_type_interpretation.csv` | `figures/failure_type_success_heatmap.png`; `tables/failure_type_results_table.tex` | Faulted subset | Failure-Class Analysis |
| Ablation study | Measure which self-healing components contribute to reliability: verifier, classifier, targeted recovery, budget, and observability. | `notebooks/self_healing_orchestrator_experiments.ipynb` | `results/ablation_summary_100_tasks.csv`; `results/ablation_results_100_tasks.csv`; `results/ablation_by_fault_intensity.csv`; `results/ablation_interpretation.csv` | `figures/ablation_success_rate_vs_fault_intensity.png`; `tables/ablation_results_table.tex` | 9000 | Ablation Study |
| Semantic silent-failure experiment | Test whether verifier-guided self-healing reduces wrong-but-plausible outputs that are syntactically valid but semantically incorrect. | `notebooks/self_healing_orchestrator_experiments.ipynb` | `results/semantic_fault_summary.csv`; `results/semantic_fault_results.csv` or equivalent semantic raw-results file if present | `figures/semantic_silent_failure_rate_vs_intensity.png`; `tables/semantic_silent_failure_table.tex` | 7200 | Semantic Silent-Failure Results |
| Trace / diagnosability analysis | Show representative recovery traces for runtime/tool failure, context/evidence failure, and semantic silent failure. | `notebooks/self_healing_orchestrator_experiments.ipynb` | `results/failure_cases_for_paper.csv` | `traces/workflow_001.json`; `traces/retrieval_001.json`; `traces/retrieval_002.json`; `tables/trace_case_studies_table.tex` | 3 representative traces | Case Studies / Diagnosability |
| Controlled budget sensitivity | Test whether self-healing still helps when recovery budget changes, and whether “just try more” explains the result. | `notebooks/self_healing_orchestrator_experiments.ipynb` | `results/budget_sensitivity_summary_overall.csv`; `results/budget_sensitivity_summary_by_intensity.csv`; `results/budget_sensitivity_results_100_tasks.csv` | `figures/budget_sensitivity_success_by_budget.png`; `figures/budget_sensitivity_success_vs_cost.png`; `tables/budget_sensitivity_table_for_latex.tex` | Varies by budget/intensity grid | Budget Sensitivity |
| Model-in-the-loop main validation | Validate whether recovery-aware orchestration remains useful when a live tool-calling model performs tool selection, argument generation, and answer synthesis over local fault-injected tools. | `notebooks/model_in_the_loop_main_experiment.ipynb` | **TBD / confirm exact committed filenames.** Expected prefix from notebook: `results/model_in_loop_main_experiment_*.csv` | **TBD / confirm exact committed filenames.** Expected prefix from notebook: `figures/model_in_loop_main_experiment_*.png`; `tables/model_in_loop_main_experiment_*.tex`; `traces/model_in_loop_main_experiment_*.json` | 90 | Model-in-the-Loop Validation |
| Model-in-the-loop supplemental: verifier ablation | Compare model-in-the-loop self-healing with and without verification. | `notebooks/model_in_the_loop_supplemental_experiments.ipynb` | **TBD / confirm exact committed filenames.** | **TBD / confirm exact committed filenames.** | 60 | Appendix / Supplemental |
| Model-in-the-loop supplemental: budget sensitivity | Measure model-in-the-loop behavior across recovery budgets. | `notebooks/model_in_the_loop_supplemental_experiments.ipynb` | **TBD / confirm exact committed filenames.** | **TBD / confirm exact committed filenames.** | 180 | Appendix / Supplemental |

---

## 3. Controlled Benchmark Artifacts

### 3.1 Main Controlled Benchmark

**Purpose:**  
Evaluate the proposed self-healing orchestrator against four baselines under controlled runtime/tool-output fault injection.

**Methods:**

- `static_workflow`
- `retry_only`
- `react_style`
- `full_replanning`
- `self_healing`

**Primary files:**

```text
results/overall_results_100_tasks.csv
results/fault_intensity_results_100_tasks.csv
results/primary_results_100_tasks.csv
results/stress_results_100_tasks.csv
```

**Expected headline values:**

- Self-healing overall success: approximately `98.8%`
- Retry-only overall success: approximately `94.5%`
- ReAct-style overall success: approximately `94.1%`
- Full replanning overall success: approximately `93.8%`
- Static workflow overall success: approximately `70.1%`

**Paper use:**  
Main comparative reliability result.

---

### 3.2 Failure-Type Analysis

**Purpose:**  
Show that targeted recovery helps across heterogeneous failure classes, not only transient retry-friendly faults.

**Primary files:**

```text
results/failure_type_results_100_tasks.csv
results/failure_type_results_100_tasks_raw.csv
results/failure_type_success_pivot.csv
results/failure_type_interpretation.csv
```

**Expected fault classes:**

- `timeout`
- `unavailable_tool`
- `malformed_output`
- `schema_drift`
- `partial_response`
- `stale_context`
- `contradictory_evidence`

**Paper use:**  
Supports the claim that failure-class-aware recovery is more robust than uniform retry or full replanning.

---

### 3.3 Ablation Study

**Purpose:**  
Identify which parts of the self-healing loop contribute most to reliability and diagnosability.

**Primary files:**

```text
results/ablation_summary_100_tasks.csv
results/ablation_results_100_tasks.csv
results/ablation_by_fault_intensity.csv
results/ablation_interpretation.csv
```

**Expected variants:**

- `self_healing_full`
- `self_healing_no_verifier`
- `self_healing_no_classifier`
- `self_healing_no_targeted_recovery`
- `self_healing_no_observability`
- `self_healing_no_budget` or relaxed-budget equivalent

**Interpretation guide:**

- Classifier removal should reduce reliability.
- Targeted recovery removal should reduce reliability.
- Verifier removal may not hurt detectable runtime faults much, but matters in semantic silent-failure settings.
- Observability removal should mainly reduce traceability, not direct task success.

---

### 3.4 Semantic Silent-Failure Experiment

**Purpose:**  
Evaluate wrong-but-plausible outputs that are syntactically valid but semantically incorrect.

**Primary files:**

```text
results/semantic_fault_summary.csv
```

Add any raw semantic result file here if present:

```text
results/<semantic_raw_results_file>.csv
```

**Expected interpretation:**

- Baselines may return wrong-but-valid answers silently.
- Self-healing without verifier may still suffer silent failures.
- Self-healing with verifier should reduce silent failures under the controlled semantic fault model.

**Important caveat:**  
These results should be described as holding **under the controlled semantic fault model**, not as a universal guarantee that verification eliminates all silent failures.

---

### 3.5 Budget Sensitivity

**Purpose:**  
Answer whether self-healing improves reliability because of targeted recovery or simply because it “tries more.”

**Primary files:**

```text
results/budget_sensitivity_summary_overall.csv
results/budget_sensitivity_summary_by_intensity.csv
results/budget_sensitivity_results_100_tasks.csv
```

**Expected interpretation:**

- More recovery budget improves all methods.
- Self-healing is strongest under constrained budgets.
- This supports the production-relevant claim that targeted recovery matters when latency/cost/recovery attempts are bounded.

---

## 4. Model-in-the-Loop Artifacts

### 4.1 Main Model-in-the-Loop Validation

**Purpose:**  
Bridge the controlled benchmark to a live-model setting.

**Experiment design:**

- Tasks: `15`
- Methods: `retry_only`, `full_replanning`, `self_healing`
- Seeds: `11`, `22`
- Fault intensity: `0.3`
- Expected rows: `15 × 3 × 2 = 90`
- Model role: live tool-calling model performs tool selection, tool argument generation, and final answer synthesis.
- Tool role: local deterministic tools.
- Fault role: controlled local tool/runtime/context faults.

**Notebook:**

```text
notebooks/model_in_the_loop_main_experiment.ipynb
```

**Expected output prefix from notebook:**

```text
model_in_loop_main_experiment
```

**Expected outputs to confirm:**

```text
results/model_in_loop_main_experiment_runs.csv
results/model_in_loop_main_experiment_summary_by_method.csv
results/model_in_loop_main_experiment_summary_by_category.csv
results/model_in_loop_main_experiment_summary_by_success_mode.csv
results/model_in_loop_main_experiment_summary_by_fault_type.csv
results/model_in_loop_main_experiment_failed_rows.csv
results/model_in_loop_main_experiment_recovery_rows.csv
results/model_in_loop_main_experiment_health_check.csv

figures/model_in_loop_main_experiment_success_by_method.png
figures/model_in_loop_main_experiment_detection_recovery_by_method.png
figures/model_in_loop_main_experiment_cost_reliability_proxy.png
figures/model_in_loop_main_experiment_success_by_category.png

tables/model_in_loop_main_experiment_summary_table.tex
tables/model_in_loop_main_experiment_by_category_table.tex
tables/model_in_loop_main_experiment_by_fault_type_table.tex
tables/model_in_loop_main_experiment_health_check_table.tex

traces/model_in_loop_main_experiment_all_traces.json
traces/model_in_loop_main_experiment_representative_trace.json

model_in_loop_main_experiment_artifact_manifest.json
```

**Current status:**  
TBD. Confirm whether these files exist under these exact names or whether they were exported under different names.

---

### 4.2 Model-in-the-Loop Supplemental Experiments

**Purpose:**  
Provide additional evidence for verifier contribution and budget sensitivity under live-model/local-tool validation.

**Notebook:**

```text
notebooks/model_in_the_loop_supplemental_experiments.ipynb
```

**Expected run sizes:**

```text
Verifier ablation: 15 tasks × 2 variants × 2 seeds = 60 runs
Budget sensitivity: 10 tasks × 3 methods × 3 budgets × 2 seeds = 180 runs
```

**Expected outputs to confirm:**

```text
results/<model_in_loop_verifier_ablation_raw_or_summary>.csv
results/<model_in_loop_budget_sensitivity_raw_or_summary>.csv

figures/<model_in_loop_verifier_ablation_figure>.png
figures/<model_in_loop_budget_sensitivity_figure>.png

tables/<model_in_loop_verifier_ablation_table>.tex
tables/<model_in_loop_budget_sensitivity_table>.tex

traces/<model_in_loop_supplemental_trace_file>.json
```

**Current status:**  
TBD. Confirm exact committed filenames.

---

## 5. Figures and Tables Map

| Paper Figure/Table | Source CSV | Output File |
|---|---|---|
| Overall reliability table | `results/overall_results_100_tasks.csv` | `tables/overall_results_table.tex` |
| Primary vs stress table | `results/primary_results_100_tasks.csv`; `results/stress_results_100_tasks.csv` | `tables/primary_stress_results_table.tex` |
| Success vs fault intensity | `results/fault_intensity_results_100_tasks.csv` | `figures/success_rate_vs_fault_intensity.png` |
| Failure-type heatmap | `results/failure_type_success_pivot.csv` | `figures/failure_type_success_heatmap.png` |
| Cost-reliability scatter | `results/overall_results_100_tasks.csv` | `figures/cost_reliability_tradeoff.png` |
| Ablation success curve | `results/ablation_by_fault_intensity.csv` | `figures/ablation_success_rate_vs_fault_intensity.png` |
| Semantic silent-failure curve | `results/semantic_fault_summary.csv` and/or semantic intensity file | `figures/semantic_silent_failure_rate_vs_intensity.png` |
| Budget sensitivity success | `results/budget_sensitivity_summary_overall.csv` | `figures/budget_sensitivity_success_by_budget.png` |
| Budget sensitivity success vs cost | `results/budget_sensitivity_summary_overall.csv` | `figures/budget_sensitivity_success_vs_cost.png` |
| Model-in-loop success by method | TBD | TBD |
| Model-in-loop detection/recovery | TBD | TBD |
| Model-in-loop cost/reliability proxy | TBD | TBD |

---

## 6. Trace Map

| Trace File | Experiment | Case | Purpose |
|---|---|---|---|
| `traces/workflow_001.json` | Controlled benchmark | Runtime/tool failure | Shows timeout recovery using retry/backoff or targeted recovery. |
| `traces/retrieval_001.json` | Controlled benchmark | Context/evidence failure | Shows stale-context detection and retrieval refresh. |
| `traces/retrieval_002.json` | Controlled benchmark | Semantic silent failure | Shows verifier-guided rejection and recovery. |
| `traces/model_in_loop_main_experiment_all_traces.json` | Model-in-loop main | TBD | Expected full trace set from model-in-loop validation. |
| `traces/model_in_loop_main_experiment_representative_trace.json` | Model-in-loop main | TBD | Expected representative model-in-loop trace. |

---

## 7. Remaining Cleanup Checklist

Use this checklist to finalize the map.

### Artifact naming

- [ ] Confirm exact model-in-loop main result file names.
- [ ] Confirm exact model-in-loop supplemental result file names.
- [ ] Confirm exact model-in-loop figure names.
- [ ] Confirm exact model-in-loop table names.
- [ ] Confirm exact model-in-loop trace names.
- [ ] Add aliases or duplicate files with clear `model_in_loop_*` names if needed.

### Documentation

- [ ] Update `README.md` to mention model-in-the-loop validation.
- [ ] Update `README.md` repository structure to list all notebooks.
- [ ] Update `README.md` artifact-scope language so it distinguishes controlled benchmark vs live-model/local-tool validation.
- [ ] Add `RUN_MANIFEST.md` or equivalent run-summary file.
- [ ] Add `MODEL_IN_LOOP_RUN_CONFIG.md` or equivalent configuration proof.

### Consistency checks

- [ ] Confirm main controlled benchmark raw row count.
- [ ] Confirm ablation raw row count.
- [ ] Confirm semantic raw row count.
- [ ] Confirm model-in-loop main row count is 90.
- [ ] Confirm model-in-loop verifier ablation row count is 60.
- [ ] Confirm model-in-loop budget sensitivity row count is 180.
- [ ] Confirm paper headline numbers match committed CSVs.
- [ ] Confirm figures/tables regenerate from committed notebooks.

---

## 8. Interpretation Boundaries

Use the following wording consistently:

**Controlled benchmark:**  
The primary benchmark isolates orchestration reliability using synthetic tasks, simulated tools, deterministic fault injection, and fixed seeds.

**Model-in-the-loop validation:**  
The model-in-the-loop validation uses a live tool-calling language model, but tools remain local/deterministic and faults remain controlled.

**Not yet evaluated:**  
The repository does not claim to evaluate production external APIs, real user traffic, naturally occurring production failures, or production retrieval infrastructure.

---

## 9. Notes for Future Revision

This artifact map should be updated whenever:

- new result files are added,
- filenames are changed,
- paper sections are renumbered,
- model-in-loop artifacts are renamed,
- new figures or tables are generated,
- a new GitHub release is created.

For the final paper artifact release, every row marked `TBD` should be replaced with the exact committed file path.
