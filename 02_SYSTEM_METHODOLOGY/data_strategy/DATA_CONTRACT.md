# Data Strategy — Dataset Roles, Contract, Split and Provenance

## 1. Selection rule

Dataset size is secondary. A dataset is selected when its **signal semantics** can test a specific hypothesis.

| Dataset | Command | Actual actuator | State/IMU | Environment | Primary role |
|---|---|---|---|---|---|
| Pelican | commanded motor speed | actual motor speed | Vicon + derived velocity/rates | indoor | command→actual actuator |
| VID | target RPM/current | measured RPM/current | IMU + mocap/RTK | force-sensor sequences | actuator + external force |
| Nano-Drone SysID | motor angular velocity input | measured/processed motor input | full state | mocap indoor | standardized motor→state benchmark |
| NanoBench | motor PWM + PID/setpoints | no measured RPM in released schema | Vicon + EKF + IMU | battery/context | command/controller/context |
| NeuroBEM | motor speed | motor speed | high-rate full dynamics | aggressive aero | residual dynamics |
| AMOVFLY | no rich low-level chain | no RPM chain | state/acceleration | wind + payload + battery | environment/OOD |

Pelican and VID provide the richest actuator chain [MSRC-005](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-005), [MSRC-007](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-007). Nano-Drone provides standardized 100-Hz/0.5-s real-flight SysID and STM32 profiling [MSRC-001](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-001). NanoBench adds 170 recordings, 27 trajectory types, Vicon, raw IMU, EKF, PWM, PID internals and battery, but its released motor fields are **PWM commands, not actual RPM** [MSRC-004](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-004). AMOVFLY contributes measured wind/context, not actuator dynamics [MSRC-009](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-009).

## 2. Canonical field contract

Every canonical signal records:

```text
name
physical meaning
source field/topic
units
frame
timestamp domain
sampling behavior
measured / derived / commanded
runtime availability
quality flags
```

Required flight metadata:

```yaml
dataset_id:
source_file:
source_sha256:
flight_id:
vehicle_id:
controller_id:
trajectory_id:
sample_rate_nominal:
timestamp_semantics:
world_frame:
body_frame:
quaternion_convention:
motor_ordering:
actuator_semantics:
units_map:
known_processing:
```

Unknown values are written as `UNKNOWN`, never guessed.

## 3. Canonical state schema

Preferred quantities:

```text
p_W[3]       world position [m]
v_W[3]       world velocity [m/s]
R_WB[3x3]    body orientation
omega_B[3]   body angular rate [rad/s]
f_B[3]       specific force [m/s^2] if available
```

Derived quantities carry a `DERIVED` flag and generating method. This matters for Pelican velocity/body rate, which are numerically derived, and NanoBench Vicon velocity, which is derived using Savitzky–Golay processing [MSRC-005](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-005), [MSRC-004](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-004).

## 4. Actuator semantics

Never merge these because they all have four channels:

```text
PWM command
normalized command
desired RPM
target current
measured RPM
measured current
estimated thrust
```

Example:

```yaml
u_cmd:
  semantic: motor_pwm
  units: count_0_65535

m_actual:
  semantic: measured_rotor_speed
  units: rad_per_s
```

## 5. Runtime availability states

Each feature receives:

```text
AVAILABLE_AT_T
AVAILABLE_WITH_KNOWN_DELAY
OFFLINE_ONLY
ORACLE_FUTURE
UNKNOWN
```

A deployable model cannot consume `ORACLE_FUTURE`.

## 6. Quality flags

At minimum:

```text
TIMESTAMP_GAP
NON_MONOTONIC_TIME
OUT_OF_RANGE
MISSING_SAMPLE
DERIVED_FROM_DIFFERENTIATION
FRAME_UNKNOWN
UNIT_UNKNOWN
INTERPOLATED_OFFLINE
FORWARD_FILLED
```

NanoBench battery is documented as 10 Hz and forward-filled into its synchronized table; that provenance must not disappear after parsing [MSRC-004](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-004). AMOVFLY's repository itself questions the stated source topic for acceleration, so the field must be audited rather than trusted by column name [MSRC-009](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-009).

## 7. Window contract

For history length $L$ and horizon $H$:

```math
\mathcal W_t=\left(Z_{t-L+1:t},U^{cmd}_{t:t+H},Y_{t+1:t+H}\right).
```

A deployable window is valid only when historical inputs satisfy source-time causality.

## 8. Split before windowing

Correct order:

```text
flight IDs
→ train / validation / test
→ fit train-only transforms
→ extract overlapping windows inside each partition
```

Group-aware splitting formalizes non-overlapping groups [MSRC-026](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-026). Random-window results are only a leakage-sensitivity diagnostic.

Primary hierarchy:

1. whole-flight held-out;
2. whole-trajectory-family held-out where possible;
3. regime-OOD held-out;
4. optional cross-airframe/cross-dataset transfer.

## 9. Train-only fitted objects

Fit using training data only:

- normalization/robust scaling;
- PCA/ZCA;
- covariance/Mahalanobis centers;
- data-selected filter parameters;
- VARX/SINDy models;
- neural hyperparameters.

A dedicated calibration split may be used for uncertainty calibration if frozen prospectively.

## 10. Statistical unit

Reduce window errors to flight level first:

```math
E_i(M)=\operatorname{aggregate}_{w\in flight_i}e_w(M).
```

Flights/groups, not windows, are the primary resampling unit.

## 11. Provenance and storage

Every experiment stores source/data/config/code identities. Recommended large-data root:

```text
/media/nahhao74/KINGSTON/AI-Project/
├── datasets/raw/
├── datasets/derived/
├── manifests/
├── experiments/
└── models/
```

Raw downloads are immutable. Git contains documentation, code/config and compact manifests, not large datasets/artifacts.
