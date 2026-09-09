# Acceptance Criteria

## 1. Purpose

Step 2 does not freeze numerical thresholds before baseline distributions are measured. It freezes **which evidence classes are required** before a mechanism can be promoted.

## 2. Validity gate

An experiment is `INVALID_EXPERIMENT` if any of the following occur:

- test leakage into normalization/PCA/ZCA/covariance/model selection;
- future information in deployable inputs;
- unverified frame/unit/quaternion convention;
- source identity or split identity missing;
- oracle future actuator used in a deployable comparison without labeling;
- runtime latency measured with incomparable boundaries;
- corrupted/misaligned source data not accounted for.

Scientific metrics are interpreted only after the validity gate passes.

## 3. Promotion rule for preprocessing

A transform is promoted if at least one of these is supported on validation and confirmed on held-out test:

- lower prediction error at similar model budget;
- similar error with materially smaller/faster model;
- improved calibration/OOD support;
- removal of a demonstrated invalid representation assumption;

and its runtime cost does not dominate the gain.

## 4. Promotion rule for explicit/symbolic core

Retain an explicit core only if it explains measurable dynamics and the **total pipeline** improves in at least one relevant Pareto dimension without unacceptable degradation in others.

## 5. Promotion rule for temporal backbone

Backbones are compared using the same information contract. A model is preferred only if it is non-dominated in the tuple:

```math
\left(E(H),T_{p99},Memory,ModelBytes,Calibration\right).
```

Mamba/SSM receives no novelty bonus; TCN/GRU receive no simplicity bonus. Selection is empirical.

## 6. Reliability acceptance

Uncertainty/support mechanisms require both:

- calibration/discrimination evidence;
- measured latency/memory overhead.

A confidence score that does not correlate with actual error/OOD risk is rejected.

## 7. Latency acceptance

Minimum runtime feasibility condition after an intended update period $T_s$ is frozen:

```math
T_{pipeline,p99}<T_s.
```

A stricter headroom target may be pre-registered later, but is not treated as a universal literature standard.

## 8. Final claim rule

A final claim must trace to:

```text
problem/gap
→ hypothesis
→ experiment contract
→ valid result
→ engineering decision
→ tested-scope claim
```

Claims may not exceed the dataset, regime, hardware or causal scope of the supporting experiment.
