# Filtering, Spectral and Statistical Conditioning Plan

## 1. Filtering policy

Filtering is not a cosmetic denoising step. Every filter changes bandwidth and delay, therefore each runtime filter must document:

- filter family/order;
- cutoff or passband;
- sample rate;
- phase behavior;
- group delay;
- causal/non-causal status.

Offline zero-phase smoothing is allowed only for diagnostics or label construction and must never be used as a deployable input transform.

## 2. Spectral audit

For relevant pairs such as command→motor and motor→motion, compute:

- Welch power spectral density;
- cross power spectral density;
- magnitude-squared coherence;
- phase;
- candidate group delay.

```math
\gamma^2_{xy}(f)=\frac{|S_{xy}(f)|^2}{S_{xx}(f)S_{yy}(f)}.
```

The goal is to identify useful dynamics bandwidth, noise/vibration bands and temporal memory before selecting receptive field or filter cutoffs.

## 3. FFT/Welch role

FFT/Welch are primarily **offline design diagnostics**, not default neural-network input features. Runtime spectral features require separate evidence because windowing itself adds memory and latency.

## 4. Standardization baseline

Default conditioning is train-only blockwise standardization:

```math
z_j=\frac{x_j-\mu_j^{train}}{\sigma_j^{train}}.
```

Blocks preserve physical meaning, e.g. inertial, actuator, translation and environment/context.

## 5. Whitening

ZCA whitening is a challenger:

```math
x_w=\Sigma^{-1/2}(x-\mu).
```

It is retained only if it improves convergence, accuracy, or enables a smaller model enough to justify preprocessing cost. Whitening does not itself reduce dimension.

## 6. PCA / truncated SVD

PCA is considered only inside physically coherent feature blocks and only after canonicalization. High variance is not equivalent to control importance, therefore global raw-feature PCA is not the default.

## 7. Mahalanobis support

Mahalanobis distance is used for compact latent/context support estimation, not on flattened high-dimensional history:

```math
d_M^2(z)=(z-\mu)^\top\hat\Sigma^{-1}(z-\mu).
```

Covariance uses shrinkage/regularization. Multimodal flight regimes may require regime-conditioned covariance instead of one global Gaussian.

## 8. Levinson–Durbin / AR option

If residual/noise is demonstrably colored and approximately stationary within a regime, an AR model estimated through Yule–Walker/Levinson–Durbin may be evaluated for residual whitening. It is not a default vehicle-dynamics model.
