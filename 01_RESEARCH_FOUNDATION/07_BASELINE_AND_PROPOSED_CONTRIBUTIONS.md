# 07 — Baselines and Proposed Contribution Candidates

## 1. Nguyên tắc

Một contribution chỉ có ý nghĩa khi baseline đủ mạnh. Không so proposed model chỉ với MLP yếu.

Baseline ladder phải đại diện cho classical linear/system-ID, pure neural temporal, hybrid physics/learning precedent, efficient compact model và oracle inputs khi cần đo information gap.

---

## 2. Baseline families

### B0 — Persistence / kinematic sanity

Ví dụ:

```math
\hat v_{t+h}=v_t,
\qquad
\hat p_{t+h}=p_t+h v_t.
```

### B1 — MIMO VARX / linear state-space

```math
y_t=
\sum_i A_i y_{t-i}
+
\sum_j B_j u_{t-j}
+
e_t.
```

Mục đích: xác định bao nhiêu dynamics giải được mà không cần deep learning.

### B2 — MLP residual / compact feed-forward

Nano benchmark có reference Res-MLP embedded model [SRC-010](sources/SOURCE_REGISTRY.md#src-010).

### B3 — GRU/LSTM

RNN multi-step precedent [SRC-003](sources/SOURCE_REGISTRY.md#src-003).

### B4 — Causal TCN

TCN precedent trong generic sequence modeling và quadrotor dynamics [SRC-032](sources/SOURCE_REGISTRY.md#src-032), [SRC-004](sources/SOURCE_REGISTRY.md#src-004).

### B5 — Mamba/SSM challenger

Mamba selective state-space [SRC-014](sources/SOURCE_REGISTRY.md#src-014), wind-conditioned quadrotor SSM precedent [SRC-015](sources/SOURCE_REGISTRY.md#src-015).

### B6 — Symbolic/sparse

SINDYc/E-SINDy [SRC-025](sources/SOURCE_REGISTRY.md#src-025), [SRC-026](sources/SOURCE_REGISTRY.md#src-026).

### B7 — Hybrid nominal + residual

Precedent từ hybrid RNN/NeuroBEM/PI-TCN [SRC-003](sources/SOURCE_REGISTRY.md#src-003), [SRC-006](sources/SOURCE_REGISTRY.md#src-006), [SRC-005](sources/SOURCE_REGISTRY.md#src-005).

---

## 3. Actuator information ladder

### A0 — Actual-actuator oracle

```math
(X_t,M^{actual}_{t:t+H})
\rightarrow
X_{future}.
```

Không deployable nếu future actual actuator chưa xảy ra. Purpose: upper-information reference.

### A1 — Direct command

```math
(X_t,U^{cmd}_{t:t+H})
\rightarrow
X_{future}.
```

Deployable input, nhưng actuator dynamics implicit.

### A2 — Proposed factorization

```math
U^{cmd}
\rightarrow
\hat M^{actual}
\rightarrow
\hat X.
```

Purpose: bridge oracle-actuation gap.

---

## 4. Contribution candidate C1 — Representation-efficient dynamics learning

Candidate claim: geometry/time/actuator canonicalization giảm nuisance variation đủ để model nhỏ hơn đạt equal/better prediction.

Evidence required: raw vs canonical ablation; fixed capacity comparison; reduced-capacity matching; yaw-rotation invariance/equivariance test; no test-statistic leakage.

---

## 5. Contribution candidate C2 — Actuation-aware Action-to-Effect World Model

```math
A_\phi:(H_t,U^{cmd})\rightarrow\hat M
```

```math
F_\theta:(H_t,\hat M)\rightarrow\hat X.
```

Motor delays and temporal alignment matter [SRC-009](sources/SOURCE_REGISTRY.md#src-009), [SRC-010](sources/SOURCE_REGISTRY.md#src-010).

Evidence required: Pelican/VID actual-vs-command analysis; actuator trajectory prediction; direct-command baseline; oracle future-actuator reference; transient/rotational regime results; timing error.

---

## 6. Contribution candidate C3 — Explicit nominal dynamics + compact residual AI

```math
f=f_{nom}+r_\theta,
\quad
f_{nom}\in\{\text{VARX,SINDYc,weak/ensemble SINDy,local incremental}\}.
```

Hybrid modeling repeatedly works in literature [SRC-003](sources/SOURCE_REGISTRY.md#src-003), [SRC-006](sources/SOURCE_REGISTRY.md#src-006); sparse discovery may produce cheap interpretable dynamics [SRC-025](sources/SOURCE_REGISTRY.md#src-025).

Evidence required: explicit core accuracy; residual energy/spectrum; neural model size reduction; total compute; whole-flight generalization.

---

## 7. Contribution candidate C4 — Data-driven temporal backbone selection

```text
explicit-core residual
       ↓
spectral / correlation audit
       ↓
GRU vs TCN vs Mamba
       ↓
accuracy–latency–memory Pareto
       ↓
selected backbone
```

Đây là engineering contribution về evidence-based architecture selection, không algorithm novelty.

---

## 8. Contribution candidate C5 — Reliability-aware output

Output contract candidate:

```math
\{\hat X(h),\hat M(h),\Sigma(h),S(h)\}.
```

Metrics: RMSE/MAE/geodesic error, NLL, empirical coverage, OOD AUROC, error–coverage curve.

---

## 9. Contribution candidate C6 — Usable prediction horizon

Working metric:

```math
H_{\text{usable}}
=
H_{\text{accurate}}
-
\tau_{\text{data}}
-
\tau_{\text{pre}}
-
\tau_{\text{infer}}
-
\tau_{\text{act/effect}}.
```

Đây là project-defined evaluation quantity. Trước khi dùng như formal contribution, Step 2 phải freeze $H_{accurate}$ definition, error threshold, latency measurement boundary và action-effect onset semantics.

---

## 10. Final selection criterion

Model cuối không nhất thiết có lowest RMSE.

Một candidate $M_i$ bị dominated nếu tồn tại $M_j$:

```math
E_j\le E_i,\quad
T_j\le T_i,\quad
Mem_j\le Mem_i
```

với ít nhất một strict inequality.

Final model phải nằm trên **Pareto frontier** và đáp ứng scientific constraints.

---

## 11. Current non-decision

```text
SELECT_TCN      = NOT_DECIDED
SELECT_GRU      = NOT_DECIDED
SELECT_MAMBA    = NOT_DECIDED
SELECT_VARX     = NOT_DECIDED
SELECT_SINDY    = NOT_DECIDED
SELECT_HGDO     = NOT_DECIDED
SELECT_UIO      = NOT_DECIDED
SELECT_ZCA      = NOT_DECIDED
SELECT_PCA      = NOT_DECIDED
```

Step 1 chỉ xác định **why test**, không xác định **what wins**.
