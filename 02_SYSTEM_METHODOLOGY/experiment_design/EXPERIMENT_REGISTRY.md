# Experiment Registry

## 1. Purpose

This registry defines the planned evidence ladder. Experiment IDs are stable; a rerun with materially changed data contract, split, metric or hypothesis receives a new experiment identity or revision record.

## 2. Experiment families

| ID | Topic | Main question |
|---|---|---|
| EXP-000 | source/schema audit | are signal semantics, timestamps, units and frames trustworthy? |
| EXP-010 | benchmark reproduction | can at least one public real-flight benchmark be reproduced before proposing improvements? |
| EXP-100 | geometry representation | does canonical geometry reduce error or required model capacity? |
| EXP-110 | timing/spectral audit | what bandwidth, delay and temporal memory are actually present? |
| EXP-120 | statistical conditioning | do ZCA/PCA/robust conditioning improve optimization or model size enough to justify cost? |
| EXP-130 | augmentation | which physically valid augmentations improve whole-flight generalization? |
| EXP-200 | actuator identification | how predictable is command→actual actuator evolution? |
| EXP-210 | actuator information ladder | oracle actual actuator vs direct command vs factorized command→actuator→state |
| EXP-300 | MIMO VARX | how much dynamics can a cheap linear MIMO model explain? |
| EXP-310 | sparse/symbolic dynamics | can weak/integral sparse discovery explain nominal nonlinear structure robustly? |
| EXP-320 | residual-memory audit | after explicit modeling, what temporal structure remains? |
| EXP-400 | temporal backbone | MLP/GRU/TCN/Mamba under matched information and compute reporting |
| EXP-410 | rollout strategy | direct multi-horizon vs recursive rollout |
| EXP-500 | hybrid WM | explicit nominal + compact temporal residual vs pure neural baselines |
| EXP-600 | disturbance/context | do wind/force/battery/payload/context features improve held-out regimes? |
| EXP-700 | uncertainty calibration | are predictive intervals calibrated by horizon/regime? |
| EXP-710 | support/OOD | can unsupported regimes and confident failures be detected? |
| EXP-800 | runtime profiling | end-to-end batch-1 mean/p95/p99/max latency, memory and model bytes |
| EXP-900 | final ablation/Pareto | which mechanisms remain non-dominated in the integrated model? |

## 3. Required experiment contract

Before running a scientific experiment, record:

```text
experiment_id
hypothesis_id
dataset/version/hash
flight split identity
input/output contract
preprocessing version
model/config identity
random seeds
metrics
acceptance/decision rule
runtime measurement boundary
```

## 4. Result states

```text
SUPPORTED_WITHIN_TESTED_SCOPE
NOT_SUPPORTED
INCONCLUSIVE
INVALID_EXPERIMENT
```

`INVALID_EXPERIMENT` is used for leakage, wrong frame/unit semantics, causal violations, broken provenance or other validity failures. It is not interpreted as model failure.

## 5. Negative results

Rejected mechanisms remain documented. Examples: whitening gives no measurable benefit; Mamba does not justify latency; symbolic core increases total cost; UIO assumptions are unsupported. Negative results are evidence, not deleted trials.
