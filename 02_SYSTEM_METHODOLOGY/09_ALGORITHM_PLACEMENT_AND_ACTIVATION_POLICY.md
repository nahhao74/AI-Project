# 09 — Algorithm Placement and Activation Policy

**Status:** `METHODOLOGY_AUTHORITY_V1.1`  
**Purpose:** xác định thuật toán nào thuộc tầng nào của project, failure mode nào kích hoạt nó, experiment nào đánh giá và khi nào phải loại bỏ.

## 1. Nguyên tắc

Project không tối ưu theo số lượng thuật toán. Mỗi mechanism phải có:

```text
failure mode
→ candidate algorithm
→ experiment
→ measurable benefit
→ compute/assumption cost
→ engineering decision
```

Nếu không tồn tại failure mode hoặc experiment kiểm chứng, thuật toán không được đưa vào runtime architecture.

## 2. Placement matrix

| Family | Algorithms | Layer | Scientific role | Activation evidence | Primary experiment |
|---|---|---|---|---|---|
| Causal validity | DAG, Tarjan SCC, reverse processing, fixed-point peeling | Data Engine | phát hiện dependency cycle và propagate invalidity | dependency graph tồn tại | EXP-000 |
| Compact predicates | bitmask, hash | Data/provenance | cheap validity state + identity | always useful for implementation | EXP-000 |
| Time-domain validity | sweep-line / interval audit | Data Engine offline | gap/overlap validity trên timeline lớn | interval overlap becomes expensive/ambiguous | EXP-000/110 |
| Geometry | $SO(3)$ log/exp, heading frame | Representation | loại coordinate nuisance/quaternion ambiguity | whole-flight equivariance test | EXP-100 |
| Spectral | FFT, Welch, CSD, coherence | Offline diagnostics | bandwidth, coherent modes, phase/delay | always diagnostic | EXP-110 |
| Time-frequency | wavelet/CWT | Offline diagnostics | transient/non-stationary frequency structure | Welch averaging hides transient structure | EXP-115 |
| Low-rank temporal | Hankel matrix, SVD, SVHT, Cadzow | Offline structure discovery | effective rank, denoising, temporal modes | low-rank singular spectrum | EXP-105/305 |
| Linear MIMO | VARX, ridge | Explicit baseline | cheap local dynamics | always baseline | EXP-300 |
| EIV-aware linear | TLS / weighted TLS | Explicit baseline challenger | input/output noise bias | regressors demonstrably noisy | EXP-300 |
| Subspace | N4SID, ERA | System-order identification | estimate state-space/order without arbitrary latent choice | Hankel singular-value structure | EXP-305 |
| Delay-aware linear | delay-DMDc / delayed VARX | Actuator/timing | classical input-delay modeling | command→effect delay measurable | EXP-205 |
| Sparse nonlinear | weak/integral/ensemble SINDy | Explicit nonlinear core | interpretable sparse nominal dynamics | residual remains structured/nonlinear | EXP-310 |
| Lifted linear | DMDc, EDMD, Koopman, Hankel-DMD/HAVOK | Explicit nonlinear challenger | nonlinear representation + cheap linear rollout | compact lifted state predicts multi-step well | EXP-330 |
| Model reduction | balanced truncation / Hankel singular values | Explicit/lifted compression | reduce state/lift dimension | high-order linear/lifted model is redundant | EXP-340 |
| Incremental | INDI-inspired local map | Representation / SysID | local action-effect relation | incremental inputs explain dynamics robustly | EXP-300/310 |
| Regime models | Piecewise/PWA, IMM challenger | Explicit dynamics | handle regime-dependent dynamics | residual distributions differ by regime | EXP-320/600 |
| Disturbance observers | HGDO, UIO | Context/observer | explicit disturbance estimate | model/rank/noise assumptions verified | EXP-600 |
| Temporal residual | MLP, ESN/NGRC, GRU, TCN, Mamba | Learned residual | model memory left after explicit core | residual-memory audit | EXP-400 |
| Rollout | recursive unfolding/unrolling | Prediction baseline | quantify compounding error | always baseline | EXP-410 |
| Multi-horizon | direct decoder | Prediction candidate | one-pass multi-horizon prediction | direct strategy improves Pareto | EXP-410 |
| Reliability | heteroscedastic head, conformal | Reliability | calibrated intervals | calibration split available | EXP-700/720 |
| Support/OOD | Mahalanobis, kNN, convex hull low-d | Reliability | detect unsupported inputs | shifted regimes available | EXP-710 |
| Timing graph | Max-Plus | Effect-time challenger | event/prerequisite timing composition | structured multi-stage delay observed | EXP-205/800 |
| Compression | quantization, pruning, distillation | Deployment | reduce runtime/model size | accurate teacher/model already exists | EXP-820 |
| Adaptation V2 | CUSUM/Page-Hinkley, RLS, low-rank update | Online adaptation | respond to drift | V1 residual drift proven | V2 only |

## 3. Causal Validity Engine

Canonical dependency processing:

```text
source signals
→ dependency DAG
→ Tarjan SCC cycle check
→ direct invalid seeds
→ reverse dependency propagation
→ fixed-point peeling
→ VALID_CAUSAL_CORE
```

This layer is deterministic and must execute before scientific windows are accepted.

### Forbidden cycle example

```text
future target
→ non-causal preprocessing
→ feature at t
→ predictor
→ future target
```

A forbidden SCC produces:

```text
CAUSAL_DEPENDENCY_CYCLE
→ INVALID_EXPERIMENT
```

## 4. Peeling has two different meanings

### 4.1 Validity peeling

Iteratively remove nodes/windows that depend on invalid or unknown prerequisites until a fixed point is reached.

```math
V_{k+1}=V_k\setminus\{v:\exists u\notin V_k,\;u\rightarrow v\}.
```

### 4.2 Residual peeling

Scientific decomposition may progressively remove explainable dynamics:

```math
r_0=y,
```

```math
r_1=r_0-\hat y_{kinematic},
```

```math
r_2=r_1-\hat r_{VARX/SINDy/Koopman}.
```

A stage is retained only if it reduces held-out residual burden without making the complete pipeline slower/worse.

## 5. Unfolding / unrolling

Recursive rollout is a required baseline:

```math
\hat X_{t+k+1}=F_\theta(\hat X_{t+k},U_{t+k}).
```

It is compared with direct multi-horizon decoding:

```math
F_\theta(H_t,U_{t:t+H})\mapsto[\hat X_{H_1},\ldots,\hat X_{H_K}].
```

The comparison tests both compounding error and repeated-compute cost.

## 6. Algorithms explicitly not active in V1

The following remain conditional because current public real-flight design does not provide the required structure:

| Algorithm | Reason not active |
|---|---|
| Möbius inversion | requires factorial/subset intervention semantics |
| MRT | randomized intervention design belongs to Pipeline 1, not observational public logs |
| MCMF | no flow-allocation failure mode identified |
| Prim/Kruskal/Chu–Liu | no graph-optimization objective requiring spanning/arborescence structure |
| DSU/small-to-large | no large dynamic-component merge problem identified |
| generic large Transformer/diffusion | added compute is not justified by current low-level latency target |

## 7. Activation states

```text
CANDIDATE
DIAGNOSTIC_ONLY
READY_TO_TEST
SUPPORTED_WITHIN_TESTED_SCOPE
NOT_SUPPORTED
INCONCLUSIVE
RETIRED
V2_ONLY
```

No algorithm can transition directly from `CANDIDATE` to `SUPPORTED_WITHIN_TESTED_SCOPE` without a registered experiment.
