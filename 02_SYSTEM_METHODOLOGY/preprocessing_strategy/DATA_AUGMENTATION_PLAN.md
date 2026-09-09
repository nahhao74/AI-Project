# Data Augmentation Plan

## 1. Principle

Augmentation is allowed only when it preserves the physical semantics of the supervised task. It must not invent actuator responses or disturbance labels that were not measured.

## 2. Geometry-preserving augmentation

Primary candidate: global yaw rotation of an entire trajectory and all frame-dependent quantities by the same angle $\alpha$.

Purpose:

- test whether canonical representation is truly yaw-invariant/equivariant;
- increase orientation coverage without changing local multirotor dynamics.

The transformation must rotate inputs and targets consistently.

## 3. Noise augmentation

Sensor-noise augmentation may be evaluated only after estimating realistic noise scale/spectrum from train flights. White Gaussian noise is not automatically a realistic IMU model.

Candidate levels are derived from measured training residuals and evaluated by whole-flight validation.

## 4. Time augmentation

Forbidden by default:

- arbitrary time warping;
- resampling that changes actuator time constants;
- command shifting that changes physical latency.

Any time-domain augmentation requires a physical justification.

## 5. Motor/action augmentation

Do not synthesize unobserved motor commands or actuator responses for causal-effect claims. Motor permutation is invalid unless airframe geometry, motor ordering and spin direction are transformed consistently.

## 6. Promotion criterion

An augmentation is retained only if it improves held-out flight/regime generalization without corrupting calibration, timing or physical consistency.
