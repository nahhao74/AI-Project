# 08 — Validation and Acceptance Plan

## 1. Decision states

```text
PROPOSED
READY_TO_TEST
SUPPORTED_WITHIN_TESTED_SCOPE
NOT_SUPPORTED
INCONCLUSIVE
INVALID_EXPERIMENT
RETIRED
SUPERSEDED
```

`INVALID_EXPERIMENT` is a contract/implementation failure and is distinct from a valid negative model result.

## 2. Invalidating conditions

- flight leakage across partitions;
- preprocessing fitted on validation/test;
- future runtime-unavailable measurement used without oracle label;
- unknown units/frame treated as known;
- non-causal preprocessing in deployable input;
- physical actuator delay shifted away in a latency experiment;
- test-set hyperparameter selection;
- architecture comparison with unequal information contracts;
- overlapping windows treated as independent statistical trials.

## 3. Predictive metrics

By horizon/variable:

- velocity/body-rate RMSE and MAE;
- $SO(3)$ geodesic attitude error;
- position error as secondary integrated quantity;
- actuator error where semantics allow;
- multi-horizon curves, not only one-step score.

```math
e_R=\cos^{-1}\!\left(\frac{\operatorname{tr}(R_{true}^\top R_{pred})-1}{2}\right).
```

## 4. Reliability metrics

- Gaussian NLL where applicable;
- empirical interval coverage;
- interval width/sharpness;
- OOD AUROC/AUPRC;
- confident-error rate;
- risk/error versus retained coverage.

A variance head without calibration evaluation is not accepted as a reliability result.

## 5. Compute metrics

- trainable parameters;
- model bytes;
- MAC/FLOP estimate;
- peak memory;
- mean/median/p95/p99/max end-to-end latency.

## 6. Statistical reporting

Primary summaries are per held-out flight: median/IQR, mean±SD as secondary, paired flight-level model differences, and bootstrap CI across flights/groups when sample count is adequate. When independent flights are too few, mark inferential claims `INCONCLUSIVE` rather than manufacturing significance.

## 7. Numeric thresholds

Step 2 does not invent universal improvement percentages before baseline distributions exist. Experiment-specific thresholds are frozen **prospectively** after baseline/data audit and before candidate test outcomes.

## 8. Final selection

A model $M_i$ is dominated if another $M_j$ satisfies:

```math
E_j\le E_i,\qquad T_j\le T_i,\qquad Mem_j\le Mem_i
```

with at least one strict inequality while meeting the same validity/reliability requirements. The final model must lie on the tested Pareto frontier.
