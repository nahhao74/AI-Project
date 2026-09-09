# Time Synchronization and Causality Plan

## 1. Objective

Synchronize logger/clock domains **without erasing physical delay**.

Observed delay is decomposed as:

```math
\tau_{obs}=\tau_{clock}+\tau_{transport}+\tau_{actuator}+\tau_{effect}.
```

Only clock/transport artifacts may be corrected by synchronization. Actuator/effect delay is part of the system to be identified.

## 2. Audit order

```text
source timestamp semantics
→ monotonicity / discontinuity check
→ clock-domain mapping
→ sample-rate/jitter audit
→ missing-data audit
→ causal resampling
→ physical lag diagnostics
```

## 3. Cross-correlation policy

Cross-correlation can be used to diagnose timing, but an estimated lag is **not automatically a clock offset**. A shift is allowed only when metadata/source semantics show that the lag is instrumentation/clock related.

For command→motor and motor→motion pairs, lag is initially treated as physical until disproven.

## 4. Causal input rule

At decision time $t$:

```math
z_t=g(\{s(\tau):\tau\le t\}).
```

No runtime feature may use future samples.

## 5. Filtering distinction

- offline label/diagnostic smoothing may be non-causal if explicitly labeled;
- runtime feature filtering must be causal;
- causal filter group delay is measured and included in latency accounting.

## 6. Timing outputs

Every qualified dataset should produce:

- nominal sample period;
- jitter distribution;
- missing-sample statistics;
- clock-offset evidence;
- command→actuator lag candidate;
- actuator→effect lag candidate;
- uncertainty of each timing estimate.
