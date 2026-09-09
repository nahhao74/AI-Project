# 03 — Module Decomposition

## MOD-01 — Source & Provenance Engine

**Responsibility:** immutable source identity, schema mapping, hashes, flight IDs and raw→derived lineage. It does not alter scientific signals.

## MOD-02 — Timing & Causality Engine

Maps clock domains and validates causal availability:

```math
t^{canonical}=f_{clock}(t^{source}).
```

It may correct clock/logger timing but must not shift command/actuator to make physical response instantaneous.

## MOD-03 — Geometry & Representation Engine

Handles units, frames, quaternion convention/continuity, heading-relative translation and relative $SO(3)$ targets.

```math
v_H=R_H^\top v_W,
\qquad
\Delta R_h=R_t^\top R_{t+h}.
```

## MOD-04 — Signal Conditioning & Spectral Audit

Runtime: causal filtering/normalization when required. Offline: Welch PSD, cross-spectrum, coherence, phase/group delay and autocorrelation. Purpose: determine useful bandwidth, noise bands and candidate memory.

## MOD-05 — Actuator Dynamics

Primary interface:

```math
U^{cmd}\rightarrow M^{actual}.
```

Candidates: first-order/time-delay, VARX/state-space, compact TCN/GRU, or explicit+residual. Outputs include future actuator trajectory and derived response-time markers.

## MOD-06 — Explicit Nominal Dynamics

Candidates: MIMO VARX, local state-space, weak/integral/ensemble SINDy, INDI-inspired local incremental map.

```math
f_{vehicle}=f_{nominal}+r_{unmodeled}.
```

Optional and removable if it does not reduce total burden.

## MOD-07 — Disturbance / Context

Possible information: battery, payload/airframe identity, measured wind, external force, generic latent context, HGDO/UIO-derived estimate when assumptions hold. Unlabeled residual is never automatically named `wind`.

## MOD-08 — Temporal Residual Model

Baseline candidates: MLP, GRU, causal/dilated TCN, depthwise causal TCN, compact Mamba/SSM challenger. Prefer to train on the residual problem after deterministic geometry and optional explicit dynamics.

## MOD-09 — Multi-Horizon Decoder

Candidate horizons begin from data-supported ranges; $\{50,100,200,500\}$ ms is an initial comparison set, not a frozen contract.

## MOD-10 — Reliability & Support

```math
(\mu_h,\Sigma_h,S_h).
```

Candidates: heteroscedastic Gaussian head, deep-ensemble challenger, conformal post-calibration, Mahalanobis/kNN support.

## MOD-11 — Runtime Profiler

Measures the complete online graph:

```text
buffer + transforms + filters + explicit core + neural model
+ decoder + reliability + reconstruction
```

Reports batch-1 mean/median/p95/p99/max, memory and model bytes.
