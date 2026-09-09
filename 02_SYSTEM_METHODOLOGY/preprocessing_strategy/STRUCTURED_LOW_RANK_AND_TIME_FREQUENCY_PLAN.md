# Structured Low-Rank and Time-Frequency Analysis Plan

## 1. Purpose

Welch/FFT describe average spectral structure but can hide transient and non-stationary behavior. Real-flight data may also contain a low-dimensional dynamical signal embedded in noisy measurements. This plan evaluates two complementary offline tools:

1. time-frequency analysis (wavelet/CWT);
2. structured low-rank Hankel analysis/denoising.

Neither is a runtime requirement by default.

## 2. Wavelet / multiresolution audit

For signal $x(t)$, continuous wavelet transform:

```math
W_x(a,b)=\frac{1}{\sqrt{|a|}}\int x(t)\,\psi^*\!\left(\frac{t-b}{a}\right)dt.
```

The goal is not decorative scalograms. EXP-115 asks whether frequency content changes materially during actuator transients, aggressive rotations or disturbance events in ways hidden by Welch averaging.

Candidate outputs:

- transient onset time;
- time-localized dominant bands;
- cross-scale energy by flight regime;
- comparison with command→motor and motor→motion timing.

Parameter choice (mother wavelet, scales, boundary handling) is diagnostic and frozen before interpreting candidate-model performance.

## 3. Hankel embedding

For a scalar sequence:

```math
H(x)=
\begin{bmatrix}
x_1 & x_2 & \cdots & x_m\\
x_2 & x_3 & \cdots & x_{m+1}\\
\vdots & \vdots & \ddots & \vdots\\
x_k & x_{k+1} & \cdots & x_{k+m-1}
\end{bmatrix}.
```

For multichannel UAV data, block-Hankel matrices are built per physical group or canonical state/action representation rather than flattening arbitrary heterogeneous channels.

SVD:

```math
H=U\Sigma V^\top.
```

The singular spectrum is used to examine effective temporal rank and to inform subspace/model-order experiments.

## 4. Low-rank denoising candidates

### 4.1 Truncated/SVHT SVD

Retain singular directions whose magnitude exceeds a train-derived threshold. The objective is to determine whether a low-rank reconstruction improves downstream identification without erasing physically meaningful fast modes.

### 4.2 Cadzow-style projection

Alternate between low-rank projection and Hankel-structure projection. This is `OFFLINE_ONLY` unless a future experiment proves a runtime benefit.

## 5. Critical risks

Low-rank denoising can remove low-energy but control-relevant signals. Therefore it cannot be justified solely by reconstruction RMSE.

EXP-105 must check:

- command→actuator coherence before/after;
- actuator transient onset before/after;
- retained singular spectrum;
- downstream VARX/N4SID/SINDy identification;
- final held-out prediction;
- whether latency-relevant fast content is attenuated.

## 6. Relationship to filtering

```text
causal filter
```

and

```text
Hankel low-rank reconstruction
```

are different hypotheses. The first limits frequency/noise according to a filter transfer function. The second assumes temporal low-rank structure.

A low-rank reconstruction must not silently replace runtime-causal preprocessing. If it uses future samples around $t$, it is `OFFLINE_ONLY`.

## 7. Promotion rule

Structured low-rank processing is promoted only if it either:

- improves identification/prediction on held-out flights;
- enables a smaller effective model order;
- improves noise robustness;

without destroying actuator/effect timing or relevant high-frequency dynamics.
