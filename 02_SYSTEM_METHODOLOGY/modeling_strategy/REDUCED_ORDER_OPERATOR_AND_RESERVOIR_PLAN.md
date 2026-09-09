# Reduced-Order, Operator and Reservoir Modeling Plan

## 1. Purpose

This plan evaluates model families that may reduce neural burden before a final temporal backbone is selected. The objective is not to maximize algorithm count, but to answer three questions:

1. what effective dynamic order is supported by the data?
2. can nonlinear dynamics be represented in a compact explicit/lifted state with cheap rollout?
3. can a lightweight recurrent surrogate outperform heavier trained recurrent models on the accuracy–latency Pareto frontier?

## 2. N4SID / ERA — dynamic order before latent dimension

Subspace identification is evaluated after canonicalization and data-validity checks. For block-Hankel input/output data, QR/SVD-based methods identify a state sequence and state-space realization:

```math
x_{t+1}=Ax_t+Bu_t,
```

```math
y_t=Cx_t+Du_t+v_t.
```

The singular-value spectrum is used as evidence for candidate order $n$ rather than selecting latent dimension only from neural precedent.

### EXP-305 questions

- Is there a stable low-order state-space representation over tested regimes?
- How rapidly do Hankel singular values decay?
- Does the identified order change materially across hover/aggressive/transient regimes?
- Does a data-supported order improve downstream model-size selection?

A single global order is not forced if evidence is strongly regime dependent.

## 3. Errors-in-Variables-aware linear identification

Ordinary least squares assumes regressors are effectively noise-free. Real-flight regressors often include noisy motor/state estimates and derived quantities. Therefore EXP-300 includes:

```text
VARX-OLS
VARX-Ridge
VARX-TLS
optional weighted/regularized TLS
```

TLS is retained only when EIV-aware modeling measurably improves held-out prediction or parameter consistency. It is not used because it is mathematically more sophisticated.

## 4. Koopman / DMDc / Hankel-DMD

Candidate lifted representation:

```math
z_t=\phi(x_t),
```

```math
z_{t+1}=Az_t+Bu_t,
```

```math
\hat x_t=g(z_t).
```

Candidate ladder:

```text
DMDc
→ delay/Hankel-DMDc
→ EDMD / learned lifting
→ stability-constrained Koopman challenger
```

The project values Koopman only if the lifted system achieves useful nonlinear multi-step prediction with a compact state and cheap matrix rollout.

### EXP-330 metrics

- per-horizon prediction error;
- lifted dimension;
- spectral radius/stability behavior;
- p99 runtime including lifting/decoder;
- memory/model bytes;
- whole-flight and regime-OOD performance.

A large neural lifting network that dominates runtime defeats the intended benefit.

## 5. Delay-aware operator models

If EXP-110/200 identifies persistent input delay, delayed VARX/DMDc is evaluated before a large neural actuator model. Delay terms remain physical model variables, not preprocessing corrections.

## 6. Model-order reduction

If N4SID/Koopman yields a high-dimensional linear/lifted model, EXP-340 evaluates reduced-order methods such as:

- Hankel singular-value truncation;
- balanced truncation where assumptions are supported;
- low-rank projection of lifted dynamics.

Selection criterion is multi-objective:

```math
\text{prediction error} + \text{runtime} + \text{memory} + \text{stability behavior}.
```

Reduced order is not accepted only because singular values are small.

## 7. Echo State Network / reservoir computing

ESN candidate:

```math
h_{t+1}=\tanh(W_rh_t+W_{in}x_t+b),
```

with primarily trained readout:

```math
\hat y_t=W_{out}h_t.
```

$W_{out}$ may be fitted by ridge regression. This creates a challenger with cheap training and potentially simple runtime updates.

EXP-400 therefore includes:

```text
MLP
ESN / NGRC challenger
GRU
causal/depthwise TCN
compact Mamba/SSM
```

ESN is promoted only if it is non-dominated after measured runtime and whole-flight accuracy.

## 8. Structure-preserving residuals

If an explicit nominal model exposes useful analytic structure, a generic residual may destroy that structure. EXP-500 may therefore include a structure-preserving residual challenger where the nominal model and downstream use make the constraint meaningful.

This mechanism is **conditional**; differential-flatness-preserving residual design is not assumed applicable to every public dataset/model interface.

## 9. Evidence basis

- N4SID: Van Overschee & De Moor, *Automatica*, 1994, DOI 10.1016/0005-1098(94)90230-5.
- EIV/TLS: Söderström, *Automatica*, 2007; Koide et al., *IFAC Journal of Systems and Control*, 2026.
- Conformal Koopman real-world validation: Hirano & Tsukamoto, 2026, https://arxiv.org/abs/2603.21580.
- Reservoir computing SysID/MPC: Williams, Kutz & Manohar, *Neural Networks*, available online 2026, DOI 10.1016/j.neunet.2026.109031.
- Reduced-order ESN: *European Journal of Control*, 2026, DOI 10.1016/j.ejcon.2026.101527.
- Flatness-preserving residual learning: Yang et al. 2025 and Hsieh et al. 2026, https://arxiv.org/abs/2504.04324 and https://arxiv.org/abs/2607.12275.
