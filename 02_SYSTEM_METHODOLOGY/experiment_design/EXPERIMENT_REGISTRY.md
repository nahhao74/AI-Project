# Experiment Registry

## 1. Purpose

This registry defines the planned evidence ladder. Experiment IDs are stable; a rerun with materially changed data contract, split, metric or hypothesis receives a new experiment identity or revision record.

## 2. Experiment families

| ID | Topic | Main question |
|---|---|---|
| EXP-000 | source/schema audit | are signal semantics, timestamps, units and frames trustworthy? |
| EXP-010 | benchmark reproduction | can at least one public real-flight benchmark be reproduced before proposing improvements? |
| EXP-100 | geometry representation | does canonical geometry reduce error or required model capacity? |
| EXP-105 | structured low-rank audit | does Hankel/SVD/SVHT/Cadzow reveal effective rank or improve noise robustness without removing relevant dynamics? |
| EXP-110 | timing/spectral audit | what bandwidth, delay and temporal memory are actually present? |
| EXP-115 | wavelet/time-frequency audit | are important transients/non-stationary modes hidden by stationary spectral summaries? |
| EXP-120 | statistical conditioning | do ZCA/PCA/robust conditioning improve optimization or model size enough to justify cost? |
| EXP-130 | augmentation | which physically valid augmentations improve whole-flight generalization? |
| EXP-200 | actuator identification | how predictable is command→actual actuator evolution? |
| EXP-205 | delay-aware classical actuation | do delayed VARX/DMDc/first-order-delay models explain actuator timing before a neural actuator model is required? |
| EXP-210 | actuator information ladder | oracle actual actuator vs direct command vs factorized command→actuator→state |
| EXP-300 | MIMO VARX / EIV baseline | how much dynamics can OLS/ridge/TLS MIMO models explain, and does input/output noise bias matter? |
| EXP-305 | subspace/model-order audit | what dynamic order is supported by Hankel/N4SID/ERA evidence? |
| EXP-310 | sparse/symbolic dynamics | can weak/integral sparse discovery explain nominal nonlinear structure robustly? |
| EXP-320 | residual-memory audit | after explicit modeling, what temporal structure remains? |
| EXP-330 | Koopman/DMDc/HAVOK challenger | can nonlinear dynamics be lifted into a compact, cheap-to-rollout linear representation? |
| EXP-340 | model-order reduction | can a linear/lifted model be reduced without unacceptable prediction or stability degradation? |
| EXP-400 | temporal backbone | MLP/ESN/GRU/TCN/Mamba under matched information and compute reporting |
| EXP-410 | rollout strategy | recursive unfolding/unrolling vs direct multi-horizon prediction |
| EXP-500 | hybrid WM | explicit nominal + compact temporal residual vs pure neural baselines; structure-preserving residual only when justified |
| EXP-600 | disturbance/context | do wind/force/battery/payload/context features improve held-out regimes? |
| EXP-700 | uncertainty calibration | are predictive intervals calibrated by horizon/regime? |
| EXP-710 | support/OOD | can unsupported regimes and confident failures be detected? |
| EXP-720 | conformal calibration | does a lightweight conformal wrapper improve empirical coverage without unacceptable interval inflation? |
| EXP-800 | runtime profiling | end-to-end batch-1 mean/p95/p99/max latency, memory and model bytes |
| EXP-820 | deployment compression | can quantization/pruning/distillation reduce runtime while preserving accepted prediction/reliability performance? |
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

Rejected mechanisms remain documented. Examples: whitening gives no measurable benefit; Mamba does not justify latency; symbolic core increases total cost; UIO assumptions are unsupported; low-rank denoising removes useful actuator transients; Koopman lifting is too expensive or unstable; ESN is dominated on the tested Pareto frontier.

## 6. Order of evidence

The default progression is:

```text
EXP-000/010
→ EXP-100/105/110/115/120/130
→ EXP-200/205/210
→ EXP-300/305/310
→ EXP-320
→ EXP-330/340 and EXP-400/410
→ EXP-500
→ EXP-600
→ EXP-700/710/720
→ EXP-800/820
→ EXP-900
```

This ordering can be shortened only when an upstream experiment demonstrates that a downstream family is unnecessary.
