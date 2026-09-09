# 01 — System Overview

## 1. Design objective

The project is a **real-flight, low-level Action-to-Effect World Model**. It predicts how a command becomes actuator motion and then vehicle motion, while exposing prediction uncertainty, empirical support and computational delay.

The architecture is decomposed so deterministic signal processing and explicit system structure handle tasks that do not require a large neural model.

## 2. Candidate architecture

```text
REAL-FLIGHT SOURCES
      │
      ▼
M01 Source / Provenance Engine
      │
      ▼
M02 Timing & Causality Engine
      │
      ▼
M03 Geometry / Representation Engine
      │
      ├──────────────► offline spectral / correlation audit
      │
      ▼
M04 Runtime Feature Stream
      │
      ├──────────────► M05 Actuator Model: Ucmd → Mactual
      ├──────────────► M06 Explicit Nominal Core: VARX / weak-SINDy / local map
      └──────────────► M07 Disturbance / Context
             │
             ▼
      M08 Temporal Residual: MLP / GRU / TCN / Mamba challenger
             │
             ▼
      M09 Multi-Horizon Decoder
             │
      ┌──────┴──────────┐
      ▼                 ▼
 future state       future actuator
      │
      ├──────────► uncertainty
      └──────────► support / abstention
             │
             ▼
      latency-accounted output
```

This is a **candidate decomposition**, not the final architecture.

## 3. Mathematical decomposition

```math
\hat M_{t:t+H}=A_\phi(H_t,U^{cmd}_{t:t+H}),
```

```math
\hat f^{nom}_t=G_\psi(Z_t,\hat M_t),
```

```math
\hat r_{t:t+H}=R_\theta(H_t,\hat M_{t:t+H},C_t),
```

```math
\hat X_{t:t+H}=\mathcal I\!\left(\hat f^{nom}+\hat r\right),
```

with reliability:

```math
(\Sigma_{t:t+H},S_{t:t+H})=Q_\eta(H_t,\hat X_{t:t+H}).
```

$Z_t$ is a canonical physical representation; $C_t$ is optional context.

## 4. What is intentionally not neural

Candidates for deterministic/explicit implementation:

- timestamp validation and clock mapping;
- units/frame conversion;
- quaternion continuity and $SO(3)$ transforms;
- causal filters;
- motor-mode transforms when geometry is known;
- standardization;
- VARX or sparse nominal dynamics;
- low-dimensional Mahalanobis support;
- effect-time extraction from predicted actuator trajectory.

The neural model is reserved for temporal/nonlinear residuals that explicit methods fail to model economically.

## 5. Two-timescale hypothesis

The system may contain fast actuation/body-rate dynamics and slower context such as battery, aerodynamics, payload or wind. This is **not assumed**. It is tested using residual autocorrelation, PSD/coherence and ablation. A slow Mamba/SSM branch is only justified if long-memory evidence remains after explicit fast dynamics are removed.

## 6. Offline versus runtime boundary

```text
OFFLINE DESIGN PATH
spectral analysis
system identification
symbolic discovery
hyperparameter search

RUNTIME PATH
buffer update
causal preprocessing
explicit core
compact temporal model
multi-horizon decoder
reliability/postprocess
```

An expensive offline search is acceptable if it produces a cheap frozen runtime model.

## 7. Evidence basis

The Nano-Drone benchmark demonstrates compact physics/residual/recurrent real-flight models and STM32-class profiling, while NanoBench adds controller commands, IMU, EKF and battery context across many trajectories [MSRC-001](sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-001), [MSRC-004](sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-004). Pelican and VID provide command/target-to-actual actuator signals required to test the actuator decomposition [MSRC-005](sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-005), [MSRC-007](sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-007).
