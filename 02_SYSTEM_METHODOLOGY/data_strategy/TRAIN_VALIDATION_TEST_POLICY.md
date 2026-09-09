# Train / Validation / Test Policy

## 1. Scientific unit of independence

Primary split unit is the **whole flight / trajectory**, not an overlapping window.

```text
flight identities
→ train / validation / test assignment
→ freeze split
→ fit train-only transforms
→ build windows inside each split
```

## 2. Prohibited split

```text
all windows
→ random_shuffle
→ train/test
```

This is invalid for primary claims because neighboring windows can share nearly identical state/action history and targets.

## 3. Split responsibilities

### Train

May fit:

- normalization statistics;
- PCA/ZCA transforms;
- covariance/shrinkage models;
- VARX/SINDy coefficients;
- neural parameters.

### Validation

May select:

- history length;
- filter candidates;
- model size;
- architecture/hyperparameters;
- uncertainty calibration parameters;
- abstention thresholds.

### Test

Used only after design decisions are frozen. It is not used for architecture or threshold selection.

## 4. Grouping hierarchy

Where dataset metadata permit, split must preserve groups such as:

```text
airframe
controller
trajectory family
speed/aggressiveness
payload
wind/environment
battery regime
```

The primary test should be at least whole-flight held-out; stronger tests intentionally hold out a regime.

## 5. Metrics aggregation

Window-level errors are first aggregated within each flight. Statistical comparison is performed across flights/trajectory groups rather than pretending all windows are independent.

## 6. Required split artifact

Every experiment must persist:

```text
split_id
random_seed
train_flight_ids
validation_flight_ids
test_flight_ids
grouping_rule
source_dataset_version
```

Changing the split creates a new experiment identity.
