# 02 — Requirements and Constraints

## 1. Requirement philosophy

Requirements are falsifiable engineering constraints. Violating a validity invariant makes an experiment `INVALID_EXPERIMENT`; it is not a scientific failure of the model.

## 2. Data validity requirements

| ID | Requirement |
|---|---|
| REQ-DATA-001 | Every window is traceable to dataset, flight ID and source hash. |
| REQ-DATA-002 | Train/validation/test assignment is by flight/trajectory group before window extraction. |
| REQ-DATA-003 | Normalizers, PCA/ZCA, covariance, symbolic models and hyperparameters are fitted without test data. |
| REQ-DATA-004 | Frame, units and quaternion convention must be known or the signal is `UNKNOWN` and excluded. |
| REQ-DATA-005 | Raw source files are immutable; derived data have transform/version identity. |
| REQ-DATA-006 | Large data/artifacts are stored under `/media/nahhao74/KINGSTON/AI-Project/`, not `/home`. |

## 3. Causality requirements

| ID | Requirement |
|---|---|
| REQ-CAUSAL-001 | Runtime input at decision time $t$ depends only on data with source time $\le t$. |
| REQ-CAUSAL-002 | Zero-phase/non-causal filtering is forbidden on runtime input features. |
| REQ-CAUSAL-003 | Future actual actuator values appear only in explicitly labeled oracle benchmarks. |
| REQ-CAUSAL-004 | Physical command→actuator/effect delay must not be removed by synchronization. |
| REQ-CAUSAL-005 | Missing-value interpolation that uses future samples is forbidden in deployable preprocessing. |

## 4. Representation requirements

| ID | Requirement |
|---|---|
| REQ-GEO-001 | Internal coordinate convention explicitly defines world/body axes and quaternion ordering. |
| REQ-GEO-002 | Quaternion sign continuity is repaired before temporal use. |
| REQ-GEO-003 | Canonical representation remains reconstructable enough for evaluation in the authoritative frame. |
| REQ-GEO-004 | Global-yaw invariance/equivariance is tested rather than assumed. |

## 5. Model-comparison requirements

| ID | Requirement |
|---|---|
| REQ-MOD-001 | Deterministic and pure-neural baselines exist before hybrid promotion. |
| REQ-MOD-002 | Architecture comparisons receive the same information unless an oracle comparison is explicitly labeled. |
| REQ-MOD-003 | Parameter count is never used as a surrogate for latency without measurement. |
| REQ-MOD-004 | Mamba/SSM is promoted only if long-memory benefit exceeds measured compute cost. |
| REQ-MOD-005 | Explicit/symbolic modules remain only if total pipeline burden/generalization improves. |

## 6. Evaluation requirements

| ID | Requirement |
|---|---|
| REQ-EVAL-001 | Primary predictive claims use held-out whole flights/trajectories. |
| REQ-EVAL-002 | Metrics are aggregated per flight before statistical comparison. |
| REQ-EVAL-003 | Final test set is not used for hyperparameter selection. |
| REQ-EVAL-004 | Rotational error uses an $SO(3)$-aware metric. |
| REQ-EVAL-005 | Uncertainty reports calibration/coverage, not only variance. |
| REQ-EVAL-006 | OOD/support is evaluated on intentionally shifted/held-out regimes. |

## 7. Compute requirements

Online feasibility:

```math
T_{\text{pipeline},p99}<T_s,
```

where $T_s$ is the intended update period.

A stricter candidate is:

```math
T_{\text{pipeline},p99}\le0.5T_s,
```

to reserve engineering headroom. The factor `0.5` is a **project candidate**, not a literature standard, and is revisited after target hardware/update rate are frozen.

## 8. Prohibited assumptions

```text
fewer parameters = faster
residual = wind
command = actuator
future actual motor sequence = deployable input
quaternion components = simple Euclidean orientation
random windows = independent samples
lower offline RMSE = automatically better control usefulness
```
