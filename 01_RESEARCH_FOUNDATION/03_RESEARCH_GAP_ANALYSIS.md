# 03 — Research Gap Analysis

## 1. Quy tắc xác định gap

Một `research gap` trong project này phải thỏa ba điều kiện:

1. có evidence rằng limitation tồn tại hoặc assumption chưa được kiểm tra;
2. limitation ảnh hưởng trực tiếp tới prediction/control usefulness;
3. có experiment khả thi trên real public data để bác bỏ hoặc ủng hộ hypothesis.

Không dùng formulation kiểu “chưa ai kết hợp A+B+C” nếu không giải thích tại sao combination đó giải một failure mode.

---

## GAP-REP-001 — Geometric representation có thể làm neural model học việc không cần thiết

### Evidence

- Nano-drone benchmark xử lý orientation trên $SO(3)$ bằng log/exp mapping thay vì filter trực tiếp quaternion [SRC-010](sources/SOURCE_REGISTRY.md#src-010).
- Wind-estimation work cho thấy data rotation/reduction cải thiện complete-flight generalization [SRC-018](sources/SOURCE_REGISTRY.md#src-018).

### Limitation

Raw world coordinates/quaternion đưa nuisance degrees of freedom vào model:

$$
(x,y,\psi)
$$

có thể thay đổi dù local physical situation tương đương.

### Candidate improvement

- heading-normalized translation;
- relative position/velocity targets;
- relative rotation:

$$
\Delta R_h=R_t^\top R_{t+h}
$$

- $SO(3)$ log-map output;
- gravity vector in body frame.

### Falsifiable test

Compare fixed parameter budgets:

$$
\text{raw representation}
\quad vs\quad
\text{canonical representation}.
$$

Gap được hỗ trợ nếu canonical form đạt lower error hoặc same error với model nhỏ hơn/nhanh hơn.

---

## GAP-ACT-001 — Future command và future physical actuator state bị conflated

### Evidence

- Pelican chứa cả commanded và actual motor speeds [SRC-020](sources/SOURCE_REGISTRY.md#src-020).
- VID chứa target RPM, measured RPM/current và force-related data [SRC-023](sources/SOURCE_REGISTRY.md#src-023).
- Motor delay được chỉ ra là end-to-end detail thường bị neglect [SRC-009](sources/SOURCE_REGISTRY.md#src-009).
- Nano benchmark phải temporal-align motor signal với acceleration [SRC-010](sources/SOURCE_REGISTRY.md#src-010).

### Limitation

Offline dynamics benchmark có thể sử dụng future actual motor sequence:

$$
M^{actual}_{t:t+H}
$$

nhưng runtime planner thường chỉ biết:

$$
U^{cmd}_{t:t+H}.
$$

Điều này tạo `oracle-actuation gap`.

### Candidate improvement

$$
U^{cmd}
\xrightarrow{A_\phi}
\hat M^{actual}
\xrightarrow{F_\theta}
\hat X.
$$

### Core comparison

1. oracle actual actuator input;
2. direct command→state;
3. proposed command→predicted actuator→state.

Nếu (3) recover đáng kể oracle accuracy trong khi giữ runtime-available inputs, gap được hỗ trợ.

---

## GAP-NOM-001 — Pure neural model có thể dùng capacity để học nominal/simple dynamics

### Evidence

Hybrid RNN, NeuroBEM và PI-TCN cho thấy structure + learning có thể outperform pure alternatives trong respective settings [SRC-003](sources/SOURCE_REGISTRY.md#src-003), [SRC-006](sources/SOURCE_REGISTRY.md#src-006), [SRC-005](sources/SOURCE_REGISTRY.md#src-005). SINDYc/E-SINDy cung cấp sparse controlled-system discovery [SRC-025](sources/SOURCE_REGISTRY.md#src-025), [SRC-026](sources/SOURCE_REGISTRY.md#src-026).

### Limitation

Pure NN phải học lại cả:

$$
\text{simple local coupling}
+
\text{memory/nonlinearity/disturbance}.
$$

### Candidate improvement

$$
\dot X=
f_{\text{explicit}}(X,U)
+
r_\theta(H_t).
$$

Candidates cho $f_{\text{explicit}}$:

- MIMO VARX;
- SINDYc;
- weak/integral or ensemble sparse discovery;
- INDI-inspired local effectiveness.

### Test

Measure:

- nominal variance explained;
- residual autocorrelation/spectrum;
- NN parameter reduction;
- H50/H100/H200/H500 error;
- inference cost.

Nếu symbolic/linear core không giảm residual complexity hoặc total latency tăng quá lớn, reject.

---

## GAP-MEM-001 — Backbone thường được chọn trước khi đo residual memory

### Evidence

- TCN là strong sequence baseline [SRC-032](sources/SOURCE_REGISTRY.md#src-032).
- Mamba có linear sequence scaling [SRC-014](sources/SOURCE_REGISTRY.md#src-014).
- Wind-Mamba study cho thấy memory-based models có lợi trong setting của họ nhưng TCN vẫn nhanh hơn base Mamba ở sequence length 32 trên RTX A6000 [SRC-015](sources/SOURCE_REGISTRY.md#src-015).

### Limitation

Architecture choice dễ trở thành trend-driven:

```text
Mamba mới hơn → chọn Mamba
```

thay vì data-driven.

### Candidate improvement

Sau explicit core, phân tích residual bằng:

- autocorrelation;
- Welch PSD;
- cross-spectrum/coherence;
- delay/group-delay diagnostics.

Chỉ dùng long-memory branch nếu residual có evidence memory dài.

### Test

GRU/TCN/Mamba cùng input contract và compute budget; select Pareto frontier.

---

## GAP-HOR-001 — Recursive multi-step rollout cộng dồn cả error lẫn compute

### Evidence

Long-horizon compounding error đã được phân tích trực tiếp [SRC-007](sources/SOURCE_REGISTRY.md#src-007). Nano benchmark chỉ ra N-step iterative evaluation scale compute theo $N T^{inf}$ [SRC-010](sources/SOURCE_REGISTRY.md#src-010).

### Candidate improvement

Direct multi-horizon output:

$$
z_t
\rightarrow
[
\hat X_{H50},
\hat X_{H100},
\hat X_{H200},
\hat X_{H500}
]
$$

hoặc direct future sequence.

### Test

Compare direct vs recursive under same horizons, training data, similar capacity, total latency and error.

---

## GAP-DIST-001 — Residual dynamics không đồng nghĩa wind

### Evidence

VID định nghĩa external force ngoài rotor thrust và gravity, còn AMOVFLY cung cấp measured wind [SRC-022](sources/SOURCE_REGISTRY.md#src-022), [SRC-024](sources/SOURCE_REGISTRY.md#src-024). Wind labels còn có spatial mismatch uncertainty theo 2026 telemetry-only work [SRC-019](sources/SOURCE_REGISTRY.md#src-019).

### Limitation

Gọi every learned residual là `wind` dẫn tới causal/semantic overclaim.

### Candidate improvement

```text
generic disturbance latent
        │
        ├── external-force supervision when available
        └── wind supervision when available
```

### Test

Evaluate each label only on datasets that actually measure it.

---

## GAP-REL-001 — Model có prediction nhưng không biết khi nào nên abstain

### Evidence

World-model survey nhấn mạnh distribution shift, uncertainty calibration và planner exploitation [SRC-001](sources/SOURCE_REGISTRY.md#src-001). Probabilistic and diffusion approaches explicitly model uncertainty [SRC-008](sources/SOURCE_REGISTRY.md#src-008), [SRC-017](sources/SOURCE_REGISTRY.md#src-017).

### Candidate improvement

Output:

$$
(\mu_h,\Sigma_h,S_h)
$$

với $S_h$ là empirical support/OOD status.

Candidate cheap support:

- latent Mahalanobis with shrinkage covariance;
- local density/kNN;
- ensemble disagreement challenger.

### Test

- calibration coverage;
- NLL;
- AUROC/OOD discrimination;
- confident-error rate;
- abstention vs retained accuracy curve.

---

## GAP-LAT-001 — Prediction horizon thường không trừ pipeline/action latency

### Evidence

Motor delay matters [SRC-009](sources/SOURCE_REGISTRY.md#src-009); embedded inference costs are non-negligible [SRC-010](sources/SOURCE_REGISTRY.md#src-010); real-time learned MPC requires careful solver/model design [SRC-012](sources/SOURCE_REGISTRY.md#src-012), [SRC-013](sources/SOURCE_REGISTRY.md#src-013).

### Candidate metric

Định nghĩa working metric:

$$
H_{\text{usable}}
=
H_{\text{accurate}}
-
\tau_{\text{data}}
-
\tau_{\text{preprocess}}
-
\tau_{\text{inference}}
-
\tau_{\text{act/effect}}.
$$

Đây là **project metric proposal**, không claim là standardized literature metric.

### Test

Một model chỉ được xem là tốt hơn cho downstream real-time use nếu accuracy gain không bị ăn hết bởi latency overhead.

---

## GAP-EVAL-001 — Random temporal split dễ đánh giá generalization quá lạc quan

### Evidence

Wind-estimation study báo cáo deterioration từ randomly selected unseen data sang complete new flights và cải thiện bằng rotation/reduction [SRC-018](sources/SOURCE_REGISTRY.md#src-018).

### Requirement

Primary split:

```text
train flights ≠ validation flights ≠ test flights
```

Additional:

- unseen trajectory;
- unseen aggressive regime;
- unseen wind/payload regime;
- optional cross-airframe transfer.

---

## 2. Gap priority

| Gap | Scientific value | Feasibility | V1 priority |
|---|---:|---:|---:|
| GAP-REP-001 | High | High | P0 |
| GAP-ACT-001 | Very high | High with Pelican/VID | P0 |
| GAP-NOM-001 | High | High | P0 |
| GAP-MEM-001 | High | High | P1 |
| GAP-HOR-001 | High | High | P0 |
| GAP-DIST-001 | Medium–High | Dataset-dependent | P1 |
| GAP-REL-001 | High | High | P1 |
| GAP-LAT-001 | Very high | High | P0 |
| GAP-EVAL-001 | Critical validity | High | P0 |

## 3. Main novelty candidate

Không claim:

> “First UAV model using TCN/Mamba/SINDy.”

Candidate contribution nếu evidence ủng hộ:

> **A real-flight, actuation-aware and representation-efficient dynamics World Model that explicitly separates command-to-actuator behavior, sparse/structured nominal dynamics and compact temporal residuals, while evaluating uncertainty/support and usable prediction horizon under whole-flight generalization.**

Novelty claim cuối cùng chỉ được phép viết sau Step 4.
