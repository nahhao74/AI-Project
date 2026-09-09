# Model Selection and Hybrid World-Model Plan

## 1. Selection principle

No temporal backbone is selected in Step 2. The methodology first removes known structure, then measures what remains.

```text
canonicalized data
→ deterministic / explicit baseline
→ residual analysis
→ temporal-memory audit
→ MLP / GRU / TCN / Mamba comparison
→ accuracy–latency–memory Pareto
```

## 2. Explicit core candidates

### MIMO VARX

```math
y_t=\sum_{i=1}^{p}A_i y_{t-i}+\sum_{j=0}^{q}B_j u_{t-j}+e_t.
```

Purpose: establish how much local MIMO dynamics can be predicted with a cheap linear model.

Orders $p,q$ are selected from train/validation evidence, informed by autocorrelation and spectral diagnostics. They are not fixed in advance.

### Sparse / symbolic dynamics

Candidate controlled sparse form:

```math
\dot X=\Theta(X,U)\Xi.
```

For noisy real-flight data, weak/integral and ensemble formulations are preferred candidates over raw finite-difference SINDy. The symbolic library is physics-guided and kept compact.

### INDI-inspired incremental representation

Candidate local relationship:

```math
\Delta\dot X\approx G_t\Delta U+d_t.
```

This is evaluated as representation/system-ID support, not as the controller of this project.

## 3. Actuator model

Primary factorization candidate:

```math
U^{cmd}_{\le t+H}\rightarrow\hat M^{actual}_{t+1:t+H}\rightarrow\hat X_{t+1:t+H}.
```

A first-order actuator model is a baseline, not an assumption:

```math
\dot\Omega_i=\frac{1}{\tau_i}(\Omega_{cmd,i}-\Omega_i).
```

A temporal actuator model is promoted only if it captures measurable state-dependent/transient behavior beyond the simple baseline.

## 4. Residual-memory audit

After explicit prediction, define:

```math
r_t=y_t-\hat y_t^{explicit}.
```

Before selecting a neural sequence model, inspect:

- ACF / partial ACF;
- Welch PSD;
- input–residual coherence;
- residual distribution by regime;
- Ljung–Box or equivalent whiteness diagnostics.

If residual is effectively memoryless, an MLP is preferred over sequence models.

## 5. Temporal candidates

### GRU

Strong recurrent/streaming baseline with compact hidden state.

### Causal TCN

Primary efficient candidate for short-to-medium memory; dilated/depthwise variants are evaluated for receptive-field efficiency.

### Mamba / selective SSM

Challenger for longer residual memory. It is promoted only if measurable prediction gain justifies actual runtime/operator cost on the tested hardware.

## 6. Multi-horizon decoder

Direct prediction is compared with recursive rollout:

```math
z_t\rightarrow[\hat X_{H_1},\hat X_{H_2},\ldots,\hat X_{H_K}].
```

Horizon candidates are selected after sample-rate and bandwidth audit.

## 7. Final hybrid candidate

```math
\hat f=\hat f_{explicit}+r_\theta(H_t),
```

possibly preceded by an actuator predictor.

The hybrid is kept only if **total** compute/memory/generalization improves. A smaller neural residual alone is insufficient evidence.

## 8. Selection objective

Model selection is Pareto-based over:

- per-horizon prediction error;
- whole-flight generalization;
- p95/p99 end-to-end latency;
- peak memory / model bytes;
- calibration/support quality;
- interpretability/assumption burden where relevant.
