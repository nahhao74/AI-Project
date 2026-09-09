# 05 — Objectives, Scope and Non-Claims

## 1. Primary objective

Xây dựng và đánh giá một **real-flight low-level dynamics World Model** có đầu vào khả dụng ở runtime, dự đoán được action-to-effect evolution trong nhiều horizon, đồng thời tối ưu trade-off:

$$
\boxed{
\text{prediction accuracy}
+
\text{generalization}
+
\text{latency}
+
\text{model complexity}
+
\text{reliability}
}
$$

Thành công của project không được định nghĩa bằng việc dùng architecture mới nhất.

---

## 2. Specific objectives

### OBJ-01 — Real-data validity

Train/validate/test bằng public **real UAV flight datasets**, không chỉ simulation.

### OBJ-02 — Causal/runtime-valid input contract

Không sử dụng future measurement mà deployment không thể biết.

Ví dụ $M^{actual}_{t+h}$ chỉ được dùng làm future input trong `oracle benchmark`, không làm primary deployable input nếu runtime chỉ biết command.

### OBJ-03 — Representation efficiency

Đánh giá geometry/time/actuator preprocessing như một mechanism có thể giảm neural learning burden.

### OBJ-04 — Actuator-aware dynamics

Mô hình hóa:

$$
U^{cmd}\rightarrow M^{actual}\rightarrow X.
$$

### OBJ-05 — Structured nominal + learned residual

Đánh giá:

$$
f=f_{\text{explicit}}+r_\theta
$$

với explicit candidates được chọn từ system identification/symbolic methods.

### OBJ-06 — Multi-horizon prediction

Report $E(h)$ theo horizon, không chỉ one-step.

### OBJ-07 — Reliability

Model phải có uncertainty/support interface và calibration evaluation.

### OBJ-08 — Deployment relevance

Measure params, model bytes, MACs/FLOPs, preprocessing latency, inference mean/p95/p99/max và peak memory khi khả thi.

---

## 3. In scope

- multirotor low-level dynamics;
- real-flight public logs;
- motor/actuator behavior khi dataset hỗ trợ;
- IMU/state/motor/control representation;
- time synchronization and causal preprocessing;
- $SO(3)$/frame canonicalization;
- spectral/system identification;
- compact neural temporal models;
- sparse/symbolic nominal models;
- disturbance context;
- uncertainty/OOD;
- multi-horizon prediction;
- deployment-oriented compute benchmarking.

---

## 4. Out of scope for V1

- vision-based navigation world models;
- language-conditioned world-action models;
- object detection/perception;
- end-to-end policy learning;
- online hardware control deployment trên UAV riêng;
- claim improved closed-loop flight performance khi chưa có control experiment;
- universal cross-airframe generalization;
- autonomous online model update;
- MRT causal intervention trên public observational data.

---

## 5. Non-claims

### NC-01 — Không claim “first World Model for UAV”

Aerial world models đã tồn tại ở visual/navigation và dynamics contexts [SRC-034](sources/SOURCE_REGISTRY.md#src-034), [SRC-001](sources/SOURCE_REGISTRY.md#src-001).

### NC-02 — Không claim TCN/Mamba/SINDy là mới

TCN [SRC-032](sources/SOURCE_REGISTRY.md#src-032), Mamba [SRC-014](sources/SOURCE_REGISTRY.md#src-014), SINDYc [SRC-025](sources/SOURCE_REGISTRY.md#src-025) đều là established methods.

### NC-03 — Không gọi residual là wind nếu không có wind ground truth

$$
r\neq W
$$

trừ khi experiment/data contract cho phép inference đó.

### NC-04 — Không claim real-time onboard chỉ từ laptop/GPU latency

Host benchmark chỉ được gọi `HOST_INFERENCE_LATENCY`. Embedded/onboard claim cần target hardware.

### NC-05 — Không claim improved real-world control

Offline real-flight predictive validity $\neq$ closed-loop real-world control improvement.

### NC-06 — Không claim causality từ observational dataset

Pipeline 2 public data có thể hỗ trợ prediction/system identification; không thay thế randomized causal action identification của một controller stack cụ thể.

---

## 6. Definition of minimum viable scientific success

Project vẫn có giá trị nếu một số candidate bị loại.

Minimum success:

1. reproduce ít nhất một public real-flight dynamics benchmark;
2. establish leakage-safe whole-flight evaluation;
3. demonstrate ít nhất một representation/decomposition ablation;
4. compare compact neural backbones fairly;
5. report accuracy–latency–complexity Pareto;
6. document negative results.

Strong success:

- actuator-aware decomposition reduces error;
- canonical representation permits smaller model;
- explicit nominal core reduces residual NN size;
- reliability gate detects unsupported regimes;
- hybrid model improves usable prediction horizon.

---

## 7. Target audience

Tài liệu phải đủ rõ cho AI/ML reviewer, controls/robotics engineer và embedded/real-time engineer. Mỗi claim phải định nghĩa signal semantics, frame, units, timing và evaluation scope, không chỉ neural architecture.
