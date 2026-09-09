# Dataset Source Notes

## 1. Dataset-selection principle

Không chọn dataset vì “lớn nhất”. Dataset được chọn theo **signal semantics cần cho hypothesis**.

---

## DATA-001 — Pelican

Source: [SRC-020](SOURCE_REGISTRY.md#src-020)

### Signals

- `Pos`: Vicon position;
- `Euler`: Vicon orientation;
- `Motors`: actual motor speeds;
- `Motors_CMD`: commanded motor speeds;
- `Vel`: derived velocity;
- `pqr`: derived body rates;
- 54 flights.

### Unique value

Có cặp:

```math
U^{cmd}
\quad\text{và}\quad
M^{actual}.
```

### Primary hypotheses

HYP-ACT-01, HYP-ACT-02, HYP-REP-01, HYP-EVAL-01.

### Known concern

Velocity/body-rate are numerically derived trong published dataset description; derivative noise/processing must be audited.

---

## DATA-002 — Nano-Drone 2026

Sources: [SRC-010](SOURCE_REGISTRY.md#src-010), [SRC-011](SOURCE_REGISTRY.md#src-011)

### Signals

- 4 motor angular velocities;
- position;
- velocity;
- quaternion orientation;
- angular velocity;
- supporting IMU/experimental data;
- ~75k samples;
- 100 Hz processed benchmark.

### Primary role

- standard benchmark reproduction;
- multi-step error;
- embedded compute;
- processing/time alignment;
- aggressive trajectories.

### Limitation

Benchmark rollout uses future motor-input sequence. For deployable planner studies, must distinguish whether future **actual motor speed** is knowable or oracle.

---

## DATA-003 — NeuroBEM

Source: [SRC-021](SOURCE_REGISTRY.md#src-021)

### Signals

At 400 Hz: position, velocity, acceleration, pose, body rates, angular acceleration, battery voltage, individual motor speeds, motor-speed derivatives.

### Value

High-rate aggressive real dynamics and aerodynamic residual context.

### Primary hypotheses

HYP-NOM-01, HYP-MEM-01, HYP-DIST-01.

### Concern

Residual force values in downstream copies must use corrected platform mass; recomputation from primitive signals is preferred.

---

## DATA-004 — VID

Sources: [SRC-022](SOURCE_REGISTRY.md#src-022), [SRC-023](SOURCE_REGISTRY.md#src-023)

### Signals

- synchronized IMU/camera;
- target rotor speeds;
- target motor currents;
- measured motor RPM/current;
- motion-capture/RTK depending sequence;
- force-sensor ground truth in dedicated sequences.

### Value

Strongest dataset for:

```math
\text{command/target}
\rightarrow
\text{actual actuator}
\rightarrow
\text{external force/state}.
```

### Concern

Vehicle-specific mass/thrust coefficient required for some dynamics/force formulations. Do not silently transfer parameters across sequences/airframes.

---

## DATA-005 — AMOVFLY

Source: [SRC-024](SOURCE_REGISTRY.md#src-024)

### Scale

- 270+ flights;
- 46.1+ hours;
- 3 UAVs;
- fixed/varying altitude and speed;
- payload variations;
- wind speed/direction;
- battery and flight state.

### Value

Environment/generalization.

### Primary hypotheses

HYP-DIST-02, HYP-EVAL-01, cross-UAV/context exploratory analysis.

### Limitation

Ready data do not provide the same detailed command/actual-motor chain as Pelican/VID.

---

## 2. Proposed evidence allocation

```text
Pelican
  └── actuator causally ordered prediction interface

Nano
  └── standardized dynamics + embedded benchmark

NeuroBEM
  └── aggressive/high-rate/aerodynamic residual

VID
  └── actuator + external force semantics

AMOVFLY
  └── real wind/environment/context diversity
```

## 3. Cross-dataset prohibition for V1

Không làm:

```text
concat(Pelican, Nano, VID, AMOVFLY)
→ one training tensor
```

trước khi có frame harmonization, signal mapping, action semantics mapping, sample-rate policy, airframe identity, controller identity và unit validation.

Cross-dataset training chỉ là later experiment nếu mapping được formally defined.
