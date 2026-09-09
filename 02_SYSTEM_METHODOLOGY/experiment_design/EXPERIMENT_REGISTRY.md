# Preprocessing Strategy — Time, Geometry, Filtering, Statistics and Augmentation

## 1. Timing: preserve physics, correct clocks

Observed delay is decomposed as:

```math
\tau_{obs}=\tau_{clock}+\tau_{logging}+\tau_{transport}+\tau_{actuator}+\tau_{plant/effect}.
```

Correct only timing components supported as clock/logger/transport artifacts. Do **not** shift away command→motor or motor→vehicle delay; those are scientific targets. The Nano-Drone benchmark provides a strong reference for source-time alignment/resampling, but its motor alignment is not copied blindly into an action-latency experiment [MSRC-001](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-001).

Runtime missing-data handling is causal: hold-last plus age/mask, or invalidate the window. Bidirectional interpolation is `OFFLINE_ONLY`.

Optional source-age feature:

```math
a_s(t)=t-t_s.
```

## 2. Internal geometry convention

Adapters convert each source into explicitly documented:

```text
R_WB : body-to-world rotation
p_W  : world position
v_W  : world velocity
omega_B : body angular rate
```

Before temporal use, quaternion ordering/convention is identified and sign continuity repaired:

```math
q_t^\top q_{t-1}<0\Rightarrow q_t\leftarrow -q_t.
```

Quaternion double-cover and Euclidean discontinuity motivate evaluating continuous alternatives [MSRC-010](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-010), with $SO(3)$ convention guidance from [MSRC-011](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-011).

## 3. Relative SO(3) target

Primary candidate:

```math
\Delta R_h=R_t^\top R_{t+h},
\qquad
r_h=\operatorname{Log}(\Delta R_h)^\vee\in\mathbb R^3.
```

Benefit: removes absolute attitude offset and quaternion sign ambiguity. Risk: log-map branch behavior near $\pi$; audit the rotation-angle distribution.

A continuous 6D rotation representation is a challenger when branch behavior matters.

## 4. Heading-relative translation

Construct a heading frame that preserves world gravity but removes arbitrary global yaw. Then:

```math
v_H=R_{WH}^\top v_W,
```

```math
\Delta p_H(h)=R_{WH}^\top(p_W(t+h)-p_W(t)).
```

A compact attitude context is the gravity direction in body coordinates:

```math
g_B=R_{WB}^\top e_z.
```

Global-yaw rotation of a whole flight becomes a deterministic equivariance unit test.

## 5. Runtime filtering versus offline smoothing

SciPy `filtfilt` is forward-backward zero-phase and therefore acausal; it is not allowed in deployable input preprocessing [MSRC-012](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-012).

Policy:

```text
runtime input     → causal SOS filter or no filter
offline labels    → zero-phase allowed when declared
spectral analysis → offline allowed when declared
```

Causal filter delay is measured and included in latency accounting.

## 6. Spectral audit

Per flight, calculate:

- Welch PSD $S_{xx}(f)$;
- CSD $S_{xy}(f)$;
- coherence:

```math
\gamma^2_{xy}(f)=\frac{|S_{xy}(f)|^2}{S_{xx}(f)S_{yy}(f)};
```

- phase/group-delay;
- ACF.

Definitions follow standard Welch/CSD/coherence references [MSRC-013](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-013).

Priority pairs:

```text
command → actual motor
actual motor → angular acceleration/body rate
actual motor → linear acceleration
wind → velocity/acceleration
battery → actuator residual
```

At 100 Hz, `nperseg ∈ {128,256,512}` with roughly 50% overlap for a Hann window is an initial diagnostic range, not a frozen parameter. Aggregate spectra per flight using median/IQR rather than blindly concatenating all flights.

A project-defined coherent-output diagnostic may use:

```math
P_{coh}(f)=\gamma^2_{xy}(f)S_{yy}(f).
```

Candidate filter/receptive-field boundaries derived from it must still pass time-domain ablation; a 95% cumulative-power rule, if tested, is explicitly a project rule rather than literature standard.

## 7. Standardization, whitening and PCA

Baseline:

```math
\tilde x_j=\frac{x_j-\mu_j^{train}}{\sigma_j^{train}}.
```

Use physical blocks: geometry/state, IMU, actuation, battery/context, environment.

Whitening is optional:

```math
z=W(x-\mu),\qquad Cov(z)\approx I.
```

ZCA/PCA definitions and trade-offs follow [MSRC-015](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-015). Whitening does **not** reduce dimension; PCA truncation does. PCA/ZCA are applied after physical decomposition, not on a giant flattened temporal window.

For covariance-dependent methods, Ledoit–Wolf shrinkage is a candidate [MSRC-016](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-016):

```math
\hat\Sigma=(1-\lambda)\Sigma_{sample}+\lambda\mu I.
```

Mahalanobis support is evaluated in a compact latent/context space:

```math
d_M^2(z)=(z-\mu)^\top\hat\Sigma^{-1}(z-\mu).
```

Its Gaussian/representation assumptions require comparison against kNN/local-density alternatives [MSRC-017](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-017).

## 8. Data augmentation

Primary physical augmentation: global yaw rotation of the **world reference frame**, consistently transforming world-frame state/orientation/targets. Body-frame IMU and motor values do not change merely because the global basis is yaw-rotated. Rotation-based wind-estimation work motivates this test [MSRC-014](../sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-014).

Test:

```text
raw + yaw augmentation
canonical without augmentation
canonical + yaw augmentation
```

Noise augmentation is calibrated from training sensor/residual statistics only.

Default OFF:

- time stretching: changes dynamics/time constants;
- mirroring: rotor geometry/spin symmetry is not automatic;
- random motor permutation;
- any augmentation using future samples.

## 9. Promotion policy

A preprocessing mechanism remains only if it lowers held-out error, permits a smaller/faster model, improves OOD support, or removes an invalid representation assumption at acceptable runtime cost.
