# 07 — Ablation Strategy

## 1. Principle

Ablation answers **what caused the improvement**. The integrated model is not scientifically interpretable if several mechanisms are introduced simultaneously.

## 2. Ablation ladder

| ID | Change | Question |
|---|---|---|
| AB-00 | deterministic/MLP baseline | Is temporal AI needed? |
| AB-10 | canonical geometry | Does representation reduce nuisance? |
| AB-20 | actuator factorization | Does command→actual modeling add value? |
| AB-30 | explicit nominal core | Can simple dynamics be removed from NN burden? |
| AB-40 | temporal residual model | Which memory architecture is Pareto-optimal? |
| AB-50 | direct multi-horizon | Does it reduce rollout error/compute? |
| AB-60 | disturbance/context | Does context help unseen regimes? |
| AB-70 | uncertainty/support | Does reliability improve under shift? |
| AB-80 | quantization/pruning later | Can runtime fall without unacceptable error? |

## 3. Controlled ablation record

Every ablation records:

- data split identity;
- preprocessing identity;
- model config;
- training seed(s);
- parameter/MAC count;
- end-to-end latency;
- per-flight metrics.

Some transforms are semantically coupled. A `canonical geometry` package may be tested first, then decomposed if it wins.

## 4. Promotion rule

A mechanism is retained if evidence supports at least one without unacceptable degradation elsewhere:

1. lower held-out-flight error;
2. equal error with materially lower runtime/memory;
3. better calibration/OOD behavior;
4. removal of an invalid assumption such as unavailable future actuator input.

## 5. Negative results are first-class

Examples:

```text
ZCA_NOT_SUPPORTED_FOR_V1
MAMBA_NOT_ON_PARETO_FRONTIER_AT_TESTED_HISTORY
HGDO_FEATURE_NO_INFORMATION_GAIN
SYMBOLIC_CORE_UNSTABLE_OR_TOO_EXPENSIVE
PCA_REMOVED_CONTROL_RELEVANT_LOW_VARIANCE_SIGNAL
```

These results remain in Step 4.

## 6. Architecture fairness

For GRU/TCN/Mamba:

- tune each family on validation;
- keep information contract identical;
- include latency and memory;
- compare stateful/cached runtime where supported;
- do not claim theoretical complexity as measured embedded speed.
