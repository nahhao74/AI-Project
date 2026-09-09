# Dataset Selection Plan

## 1. Principle

Dataset selection is driven by **signal semantics required by each hypothesis**, not by dataset size. A dataset may be strong for one module and invalid for another.

## 2. Dataset roles

| Dataset | Signals that matter | Primary use | Explicit limitation |
|---|---|---|---|
| Pelican | commanded motor speed, actual motor speed, Vicon pose, derived velocity/body rate | command→actuator→state factorization | velocity/body rate are numerically derived; derivative quality must be audited |
| VID | target RPM/current, measured RPM/current, IMU, motion capture/RTK, force-sensor sequences | actuator validation; external-force supervision | vehicle-specific mass/thrust parameters matter for some force formulations |
| Nano-Drone SysID 2026 | actual motor angular velocity, full state, 100 Hz standardized benchmark | baseline reproduction; multi-step prediction; STM32 profiling | future motor sequence is an oracle-like input for deployable command-side planning |
| NanoBench 2026 | 170 flights, Vicon, IMU, EKF, per-motor PWM, PID internals, battery | command-side dynamics, controller context, battery/regime analysis | PWM command is not actual rotor speed |
| NeuroBEM | 400 Hz aggressive flight, motor speed, battery, high-rate state derivatives | aggressive/aerodynamic residual and memory analysis | platform-specific aerodynamic regime |
| AMOVFLY | wind speed/direction, state, battery, payload, 3 UAVs | environment/wind/OOD and cross-UAV context | lacks the same command→actual-motor richness as Pelican/VID |

## 3. Dataset qualification states

```text
DISCOVERED
SCHEMA_AUDITED
TIMING_AUDITED
FRAME_AUDITED
SCIENTIFIC_ROLE_APPROVED
READY_FOR_EXPERIMENT
REJECTED_FOR_ROLE
```

A dataset can be `READY_FOR_EXPERIMENT` for one scientific role and `REJECTED_FOR_ROLE` for another.

## 4. Signal-role rule

Example:

```text
NanoBench motor PWM
  → valid as command-side input
  → invalid as evidence of actual actuator response

Pelican Motors_CMD + Motors
  → valid for explicit command→actual actuator modeling
```

This distinction is mandatory for HYP-ACT experiments.

## 5. Cross-dataset training prohibition for V1

Do not concatenate datasets until all mappings below are explicit:

- coordinate frame;
- quaternion convention;
- units;
- sample rate;
- timestamp semantics;
- actuator meaning and scaling;
- controller identity;
- airframe identity;
- ground-truth source.

Cross-dataset transfer is a later experiment, not a preprocessing shortcut.
