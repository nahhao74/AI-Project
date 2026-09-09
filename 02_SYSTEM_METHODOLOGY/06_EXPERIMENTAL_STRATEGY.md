# 06 — Experimental Strategy

## 1. Experimental sequence

```text
E0 source/data validity
→ E1 public benchmark reproduction
→ E2 representation/time/spectral audit
→ E3 actuator decomposition
→ E4 explicit nominal identification
→ E5 residual-memory audit
→ E6 temporal model comparison
→ E7 hybrid integration
→ E8 disturbance/context
→ E9 uncertainty/OOD
→ E10 latency/Pareto
```

The sequence prevents building a complex model before proving simpler assumptions.

## 2. Reproduction first

Before claiming an improvement on the Nano-Drone benchmark, reproduce at least one official reference baseline with the published trajectory split and metric horizon [MSRC-001](sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-001), [MSRC-002](sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-002).

A failed reproduction is an engineering blocker, not evidence that the proposed model is better.

## 3. Representation experiment

Compare:

```text
R0 raw world/quaternion
R1 sign-continuous quaternion + normalized units
R2 heading-relative translation + relative SO(3)
R3 R2 + physics-lite actuator features
R4 optional blockwise whitening/PCA
```

Use the same flight splits and comparable model budget. Primary question: can a smaller model on a better representation match or beat a larger raw-input model?

## 4. Actuator experiment

On Pelican/VID:

```text
A0 oracle actual future actuator
A1 direct future command → state
A2 command → predicted actuator → state
```

Report actuator-trajectory error and downstream state error.

## 5. Nominal-model experiment

```text
N0 persistence/kinematic
N1 MIMO VARX
N2 weak/integral sparse symbolic
N3 pure neural
N4 explicit nominal + compact neural residual
```

Residual diagnostics decide whether temporal learning remains justified.

## 6. Temporal model experiment

Compare MLP, GRU, causal TCN and compact Mamba under the same canonical input, same partitions, same horizons and measured batch-1 latency. Do not force unnatural exact parameter equality; report the accuracy–latency Pareto curve.

## 7. Reliability experiment

Create intentionally shifted evaluation conditions: unseen trajectory family, held-out aggressiveness, wind/payload bin, or independent airframe/dataset when semantics permit. Evaluate calibration, OOD ranking and error–coverage.

## 8. Statistical unit

The primary independent unit is a flight/trajectory, not a window. For the same held-out flights:

```math
d_i=E_i(A)-E_i(B),
```

where $i$ indexes flights. Confidence intervals resample flights/groups; overlapping-window i.i.d. bootstrap is not primary evidence.
