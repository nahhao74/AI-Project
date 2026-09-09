# 04 — Research Questions and Falsifiable Hypotheses

## 1. Nguyên tắc

Mỗi hypothesis phải có khả năng bị bác bỏ. Step 1 không freeze numerical threshold nếu chưa có baseline distribution; threshold sẽ được pre-register ở Step 2 sau baseline reproduction/data audit.

Status hiện tại của tất cả hypothesis:

```text
PROPOSED_NOT_YET_TESTED
```

---

## RQ-1 — Representation

### Question

Representation hình học được canonicalize có làm learning problem đơn giản hơn raw world-frame/quaternion representation không?

### HYP-REP-01

$$
E(\text{canonical},P)
<
E(\text{raw},P)
$$

với cùng parameter budget $P$, **hoặc**

$$
P_{\text{canonical}}
<
P_{\text{raw}}
$$

at equivalent error.

Candidate transforms: heading-relative translational frame; relative displacement/velocity; $SO(3)$ relative rotation; quaternion log/exp representation; body gravity vector; motor-mode features.

### Failure condition

Nếu canonicalization không cải thiện error/size/latency và tạo preprocessing overhead đáng kể → reject mechanism.

---

## RQ-2 — Actuation

### HYP-ACT-01

Cho:

$$
\mathcal M_D:(X,U^{cmd})\rightarrow X_f
$$

và:

$$
\mathcal M_A:U^{cmd}\rightarrow\hat M^{actual}\rightarrow X_f,
$$

hypothesis là:

$$
E(\mathcal M_A)<E(\mathcal M_D)
$$

đặc biệt ở rotational/transient regimes.

### HYP-ACT-02 — Oracle-gap recovery

So với oracle:

$$
\mathcal M_O:(X,M^{actual}_{future})\rightarrow X_f,
$$

đo:

$$
R_{\text{recover}}
=
\frac{E_D-E_A}{E_D-E_O}.
$$

$R_{\text{recover}}>0$ nghĩa explicit actuator model recover một phần oracle advantage bằng runtime-available command.

### HYP-ACT-03 — Timing

Predicted actuator trajectory cho phép estimate response markers:

$$
T_{10},T_{50},T_{90}
$$

với error đủ nhỏ để cải thiện usable horizon.

---

## RQ-3 — Explicit/symbolic nominal dynamics

### HYP-NOM-01

Một explicit model:

$$
f_{nom}\in
\{VARX,SINDYc,weak/ensemble\ SINDy,INDI\text{-inspired local map}\}
$$

giải thích measurable fraction của short-horizon dynamics.

Metric candidates:

$$
R^2_{nom},
\quad
\frac{\mathbb E\|r\|^2}{\mathbb E\|\dot X\|^2}.
$$

### HYP-NOM-02

Hybrid:

$$
f=f_{nom}+r_\theta
$$

đạt same/better prediction với residual NN nhỏ hơn pure NN.

### Failure condition

Nếu total compute của explicit+NN lớn hơn pure NN và error không giảm meaningful → reject hybrid mechanism.

---

## RQ-4 — Temporal backbone

### HYP-MEM-01

Residual spectrum/autocorrelation có measurable temporal structure; memoryless MLP không đủ.

### HYP-MEM-02

Nếu residual correlation tập trung trong short receptive field, compact causal TCN/GRU nằm trên Pareto frontier hơn Mamba.

### HYP-MEM-03

Mamba chỉ được promote nếu long-context residual prediction giảm error đủ lớn so với TCN/GRU để bù runtime overhead.

Không có hypothesis “Mamba sẽ tốt hơn”. Architecture selection là outcome, không premise.

---

## RQ-5 — Multi-horizon rollout

### HYP-HOR-01

Direct multi-horizon decoder giảm $E(H)$ hoặc total inference time so với iterative autoregressive rollout ở same requested horizons.

Horizon candidates:

$$
H=\{50,100,200,500\}\;\text{ms}
$$

sẽ được điều chỉnh theo dataset sample rate/bandwidth ở Step 2.

---

## RQ-6 — Disturbance context

### HYP-DIST-01

Một generic disturbance/context latent cải thiện unseen-regime prediction so với state/action-only model khi real data chứa sufficient excitation.

### HYP-DIST-02

Explicit wind supervision chỉ cải thiện wind-conditioned prediction trên dataset có measured wind; không được generalize claim sang unlabeled residuals.

### HYP-DIST-03

HGDO/UIO-derived features chỉ được giữ nếu tạo information gain sau khi account sensor noise/model assumptions.

---

## RQ-7 — Reliability

### HYP-REL-01

Predicted uncertainty có empirical calibration:

$$
P(Y\in CI_{1-\alpha})\approx1-\alpha.
$$

### HYP-REL-02

Latent support/OOD score correlate với large prediction error và phát hiện unseen regimes tốt hơn random confidence.

### HYP-REL-03

Abstention cải thiện retained-set reliability:

$$
Error(\text{retained after abstention})
<
Error(\text{all predictions})
$$

với coverage được report cùng error.

---

## RQ-8 — Latency and deployability

### Working metric

$$
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
\tau_{\text{effect}}.
$$

### HYP-LAT-01

Model có lower RMSE nhưng compute quá chậm **không** mặc định tốt hơn; final selection theo accuracy–latency Pareto.

### HYP-LAT-02

Structured preprocessing/explicit dynamics cho phép giảm neural parameter/MAC count mà không giảm usable accuracy.

### HYP-LAT-03

End-to-end preprocessing + inference p95/p99 phải được đo riêng với pure `model.forward()`.

---

## RQ-9 — Evaluation validity

### HYP-EVAL-01

Random-window evaluation cho error thấp hơn whole-flight held-out evaluation do temporal similarity/leakage risk.

### Requirement

Primary claims phải dựa trên whole-flight/trajectory split.

---

# 2. Hypothesis → planned evidence

| Hypothesis | Primary evidence dataset candidate | Main metric |
|---|---|---|
| HYP-REP-01 | Nano/Pelican | error vs params/latency |
| HYP-ACT-01 | Pelican/VID | actuator + state error |
| HYP-ACT-02 | Pelican/VID | oracle-gap recovery |
| HYP-NOM-01 | Nano/NeuroBEM | variance explained/residual |
| HYP-NOM-02 | Nano/Pelican | error vs NN size |
| HYP-MEM-* | residuals from above | PSD/ACF + model Pareto |
| HYP-HOR-01 | Nano/Pelican | RMSE(h) + total latency |
| HYP-DIST-* | VID/AMOVFLY/NeuroBEM | force/wind/OOD error |
| HYP-REL-* | all held-out regimes | calibration/OOD |
| HYP-LAT-* | host first, later target hardware | p95/p99, memory, MACs |
| HYP-EVAL-01 | Pelican/AMOVFLY | random vs whole-flight gap |

# 3. Scientific decision rule

Sau Step 4, mỗi hypothesis chỉ nhận một trong:

```text
SUPPORTED_WITHIN_TESTED_SCOPE
NOT_SUPPORTED
INCONCLUSIVE
INVALID_EXPERIMENT
```

Không dùng `PASS` nếu experiment không thể phân biệt mechanism effect với confounder.
