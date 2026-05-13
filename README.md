# Self-Healing Agentic Orchestrator

This repository contains the benchmark, experiment code, result files, figures, and representative traces for the paper:

**Self-Healing Agentic Orchestrators for Reliable Tool-Augmented Large Language Model Systems**

## Overview

The paper studies reliability in tool-augmented large language model systems as an orchestration-level control problem. The proposed self-healing orchestrator implements a monitor-detect-diagnose-recover-verify loop for handling runtime failures, tool-output failures, stale context, contradictory evidence, and semantic silent failures.

The experiments use a controlled benchmark with simulated tools and deterministic fault injection to compare:

- Static workflow orchestration
- Retry-only orchestration
- ReAct-style orchestration
- Full-replanning orchestration
- Self-healing orchestration

## Repository Structure

```text
notebooks/   Main experiment notebook
data/        Benchmark task definitions
results/     Result CSV files and representative traces
figures/     Generated figures used in the paper
