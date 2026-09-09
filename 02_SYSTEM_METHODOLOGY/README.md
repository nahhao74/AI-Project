# Step 2 — System Methodology

**Methodology status:** `COMPLETE_V1`  
**Evidence cutoff:** 2026-09-09  
**Authority:** Step 2 defines the research execution contract; it does **not** select a final World Model architecture.

## 1. Purpose

Step 1 established *why* a real-flight Action-to-Effect World Model is worth investigating. Step 2 defines *how* the project will test those hypotheses without leaking future information, conflating command with physical actuation, or selecting algorithms by trend.

```text
signal semantics
→ causal data contract
→ representation
→ diagnostic analysis
→ explicit/learned model candidates
→ controlled ablation
→ whole-flight validation
→ accuracy–latency–reliability decision
```

A mechanism is promoted only if a pre-defined experiment shows that it resolves a specific failure mode at acceptable computational cost.

## 2. Working system formulation

```math
H_t=\{X_{\le t},I_{\le t},U^{cmd}_{\le t},M^{actual}_{\le t}\;\text{if measured},C_{\le t}\},
```

where $I$ denotes inertial signals and $C$ optional context such as battery, controller state, payload or measured environment.

```math
\mathcal{M}_\theta:(H_t,U^{cmd}_{t:t+H})\mapsto(\hat X_{t+1:t+H},\hat M_{t+1:t+H},\Sigma_{t+1:t+H},S_{t+1:t+H}).
```

The methodology distinguishes:

```text
ORACLE: future actual actuator is supplied
DEPLOYABLE DIRECT: future command is supplied; actuator is implicit
DEPLOYABLE FACTORIZED: future command → predicted actuator → predicted vehicle effect
```

The oracle is an information upper bound, not a deployable primary model.

## 3. Methodology layers

```text
L0  Source identity & provenance
L1  Timestamp / clock / causality audit
L2  Frame, unit & geometry canonicalization
L3  Runtime-valid filtering & missing-data handling
L4  Spectral / correlation / system-ID diagnostics
L5  Explicit actuator / nominal-model candidates
L6  Compact temporal residual candidates
L7  Multi-horizon decoding
L8  Uncertainty / support / abstention
L9  End-to-end latency & compute accounting
L10 Whole-flight / regime-OOD verification
```

FFT/Welch/coherence are primarily **offline diagnostics** for bandwidth and memory selection; they are not automatically runtime neural features.

## 4. Dataset roles

| Dataset | Primary methodological role |
|---|---|
| Pelican | commanded motor speed → actual motor speed → vehicle state |
| VID | target RPM/current → measured RPM/current; external-force sequences |
| Nano-Drone SysID 2026 | standardized actual-motor → state benchmark; STM32 profiling |
| NanoBench 2026 | 170 flights, PWM/controller/IMU/EKF/battery context |
| NeuroBEM | 400 Hz aggressive dynamics and aerodynamic residual context |
| AMOVFLY | wind/environment/payload/cross-UAV context |

Blind concatenation is forbidden until units, frames, actuator semantics and airframe identities have an explicit mapping.

## 5. Critical methodology decisions

### MD-01 — Split before fitting preprocessing

Flight/trajectory identity is split into train/validation/test **before** fitting normalization, PCA/ZCA, covariance, symbolic equations or model hyperparameters.

### MD-02 — Do not time-shift away physical actuator delay

Clock/logger offsets may be corrected. Physical command→actuator and actuator→effect delay remain observable:

```math
\tau_{observed}=\tau_{clock}+\tau_{transport}+\tau_{actuator}+\tau_{effect}.
```

### MD-03 — Runtime input path is causal

Offline zero-phase smoothing may be used for label construction/diagnostics when explicitly marked; runtime-valid input transforms may not use samples after $t$.

### MD-04 — Geometry is handled outside the neural model when possible

Heading-relative translation, relative $SO(3)$ rotation and explicit frame/unit conventions are primary candidates. Raw quaternion remains a baseline.

### MD-05 — Complexity reduction is measured end-to-end

A hybrid method is not lighter merely because the neural network is smaller. Preprocessing, explicit/observer computation and postprocessing all count.

## 6. Document map

| Topic | Authority |
|---|---|
| System architecture | `01_SYSTEM_OVERVIEW.md` |
| Requirements/invariants | `02_REQUIREMENTS_AND_CONSTRAINTS.md` |
| Module responsibilities | `03_MODULE_DECOMPOSITION.md` |
| Data/information flow | `04_DATA_FLOW_AND_INFORMATION_FLOW.md` |
| Latency/compute | `05_LATENCY_AND_COMPUTE_BUDGET.md` |
| Experiment strategy | `06_EXPERIMENTAL_STRATEGY.md` |
| Ablation ladder | `07_ABLATION_STRATEGY.md` |
| Validation/decision states | `08_VALIDATION_AND_ACCEPTANCE_PLAN.md` |
| Data strategy | `data_strategy/` |
| Preprocessing strategy | `preprocessing_strategy/` |
| Modeling strategy | `modeling_strategy/` |
| Experiment contracts | `experiment_design/` |
| Methodology sources | `sources/METHODOLOGY_SOURCE_REGISTRY.md` |

## 7. Current non-decisions

```text
FINAL_SAMPLE_RATE         = NOT_SELECTED
FINAL_HISTORY_LENGTH      = NOT_SELECTED
FINAL_FILTER_CUTOFFS      = NOT_SELECTED
FINAL_ROTATION_REP        = NOT_SELECTED
WHITENING                 = NOT_SELECTED
VARX_ORDER                = NOT_SELECTED
SYMBOLIC_LIBRARY          = NOT_SELECTED
TCN / GRU / MAMBA         = NOT_SELECTED
HGDO / UIO                = NOT_SELECTED
UNCERTAINTY / OOD METHOD  = NOT_SELECTED
TARGET_HARDWARE           = NOT_SELECTED
```

Step 2 freezes **how these decisions are made**, not their outcomes.
