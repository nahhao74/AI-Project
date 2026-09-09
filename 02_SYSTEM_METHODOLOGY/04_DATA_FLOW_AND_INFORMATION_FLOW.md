# 04 — Data Flow and Information Flow

## 1. Raw-to-model lineage

```text
downloaded source
  ↓ SHA256 + source metadata
immutable raw file
  ↓ parser/version
typed per-dataset table
  ↓ flight-level split
train / validation / test identities
  ↓ train-only fit transforms
canonical causal stream
  ↓ windows
model inputs / targets
  ↓ experiment config hash
results / plots / decisions
```

No derived file replaces the raw authority.

## 2. Time domains

Each signal record preserves where available:

```text
timestamp_source
timestamp_host/receipt
sampling source
flight/session ID
sensor/controller origin
```

Missing timing semantics are recorded, not invented.

## 3. Canonical signal classes

### State

- world position $p_W$;
- world velocity $v_W$;
- orientation $R_{WB}$;
- body angular rate $\omega_B$;
- optional specific force/acceleration.

### Actuation

`PWM command`, `normalized command`, `desired RPM`, `target current`, `measured RPM`, `measured current` and `estimated thrust` are separate semantics, never shape-compatible aliases.

### Context

Battery, payload/vehicle/controller identity, measured wind/external force when available.

## 4. Deployable information frontier

At prediction time $t$:

```math
\mathcal I_t^{runtime}=\{s:\;t_s\le t\land s\text{ is actually available online}\}.
```

Future command may be included if a downstream planner genuinely knows the planned sequence. Future measured RPM/sensor values are not runtime information.

## 5. Missing modality policy

Datasets are not forced into one tensor by fabricating signals. A model may be dataset-specific, use an explicit modality mask, or be transferred only after semantics are mapped. `missing` is different from `zero`.

## 6. Relative targets

Preferred targets reduce nuisance:

```math
\Delta p_H(h)=R_H^\top\left(p_W(t+h)-p_W(t)\right),
```

```math
\Delta R_h=R_t^\top R_{t+h}.
```

Rotation output uses an $SO(3)$ log-map or continuous representation challenger and is reconstructed for evaluation in the authoritative frame.

## 7. Actuator timing derived from trajectory

If $\hat M(t:t+H)$ is predicted, $T_{10},T_{50},T_{90}$ can be extracted by threshold crossing rather than a separate large latency network. Exact steady/peak definitions are frozen in the actuator experiment contract.
