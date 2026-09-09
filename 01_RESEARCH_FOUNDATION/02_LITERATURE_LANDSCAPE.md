# 02 — Literature Landscape

## 1. Mục đích review

Review này không nhóm paper theo “AI architecture mới nhất”, mà theo **failure mode của dynamics World Model**:

1. multi-step prediction;
2. structural/hybrid modeling;
3. actuator dynamics and timing;
4. disturbance/wind adaptation;
5. uncertainty and OOD;
6. real-time/embedded feasibility;
7. symbolic/sparse discovery;
8. real-flight dataset availability.

Cách tổ chức này tránh việc chọn algorithm trước khi hiểu bài toán.

---

## 2. Multi-step learned quadrotor dynamics

### Mohajerin et al. 2018 / Mohajerin & Waslander 2019

Các công trình dựa trên Pelican dataset sử dụng recurrent neural networks cho multi-step prediction và hybrid simplified physics + RNN [SRC-002](sources/SOURCE_REGISTRY.md#src-002), [SRC-003](sources/SOURCE_REGISTRY.md#src-003). TNNLS 2019 báo cáo hybrid model giữ velocity prediction gần measurements tới khoảng 1.9 s trên test data trong setting của họ.

**Điều đã được chứng minh:** sequence memory có ích; RNN initialization quan trọng; simplified physics + learned component có thể tốt hơn pure black-box; long-horizon evaluation phải được xem riêng với one-step.

**Khoảng trống đối với project:** không tập trung explicit command→actual actuator decomposition; uncertainty/support và embedded latency không phải primary target.

### End2End-TCN

End2End-TCN sử dụng temporal convolutions và future control sequence để dự đoán multi-step trong một forward pass [SRC-004](sources/SOURCE_REGISTRY.md#src-004).

**Giá trị:** direct sequence prediction là baseline bắt buộc; future action conditioning đã có precedent; không thể claim novelty từ “future action + TCN”.

### Long-horizon dynamics 2024

Rao et al. phân tích architecture, history length và multi-step loss trên real quadrotor data; sequential modeling giảm compounding error và decoupled dynamics learning giúp đơn giản hóa problem [SRC-007](sources/SOURCE_REGISTRY.md#src-007).

**Implication:** contribution mới phải tách khỏi contribution đã biết của multi-step training.

---

## 3. Physics/hybrid learned dynamics

### NeuroBEM

NeuroBEM kết hợp first-principles blade-element model với learned aerodynamic residual, đánh giá trên aggressive real flight tới khoảng 65 km/h và báo cáo giảm prediction error so với các baseline của họ [SRC-006](sources/SOURCE_REGISTRY.md#src-006).

**Strength:** strong real-flight evidence; residual dynamics thay vì học toàn bộ plant; aerodynamic effects được xem là failure mode chính ở aggressive flight.

**Limit relative to project:** cần nhiều platform-specific physics/parameters hơn hướng public-dataset modular; không đặt command→actuator timing làm interface chính.

### PI-TCN

PI-TCN nhúng physics constraints vào temporal convolutional network và tích hợp learned model trong MPC [SRC-005](sources/SOURCE_REGISTRY.md#src-005).

**Implication:** `physics-informed loss + TCN` đã tồn tại; physics-lite/canonical structure của project chỉ có giá trị nếu giảm assumptions/cost hoặc tăng generalization.

### PI-WAN

PI-WAN dùng TCN + physics-informed loss để thích nghi với wind/payload/external perturbations và báo cáo simulation + real-flight experiments [SRC-016](sources/SOURCE_REGISTRY.md#src-016).

**Implication:** `wind-aware TCN` không phải novelty đủ riêng.

---

## 4. Uncertainty-aware dynamics

### DroneDiffusion

DroneDiffusion mô hình dynamics như conditional diffusion sequence generation để capture multimodal uncertainty; paper báo cáo simulation và real-world flights dưới varying trajectories/payload/wind [SRC-008](sources/SOURCE_REGISTRY.md#src-008).

**Trade-off:** uncertainty richness đổi lấy generative inference complexity; embedded 100 Hz applicability phải được đo chứ không suy đoán.

### UL-MPC 2026

UL-MPC dùng probabilistic residual neural network xuất mean + covariance và dùng uncertainty trong chance-constrained MPC; paper available online tháng 8/2026 báo cáo high-fidelity Gazebo validation, chưa phải real-flight evidence [SRC-017](sources/SOURCE_REGISTRY.md#src-017).

**Implication:** uncertainty phải có calibration và scope rõ.

---

## 5. Actuator dynamics và delay

### Data-driven motor delay identification

Eschmann et al. 2024 xác định motor delays là low-level detail thường bị bỏ qua dù quan trọng cho end-to-end control, và derive MAP-based estimation của latent first-order motor time constant từ proprioceptive flight data [SRC-009](sources/SOURCE_REGISTRY.md#src-009).

Đây là nguồn trực tiếp cho:

\[
U^{cmd}\rightarrow M^{actual}\rightarrow X.
\]

### Nano-drone benchmark 2026

Benchmark dùng khoảng 75k real-world samples trên Crazyflie 2.1 Brushless, synchronized four motor inputs và 13-dimensional output, đánh giá open-loop prediction tới 0.5 s [SRC-010](sources/SOURCE_REGISTRY.md#src-010), [SRC-011](sources/SOURCE_REGISTRY.md#src-011).

Các chi tiết quan trọng:

- resample 100 Hz;
- motor–acceleration alignment bằng cross-correlation;
- signal-dependent low-pass filtering;
- quaternion log/exp map để filter trên manifold hợp lý;
- embedded inference benchmark trên STM32;
- reference physics, residual MLP, hybrid, residual LSTM models.

Trong benchmark, per-step inference được báo cáo trên target STM32 xấp xỉ:

| Model | Trainable params | Average inference / step |
|---|---:|---:|
| Res-MLP | 18.5K | 1.03 ms |
| Res-LSTM | 24.7K | 2.09 ms |
| Physics | 0 | 1.79 ms |
| Hybrid | 18.5K | 2.82 ms |

[SRC-010](sources/SOURCE_REGISTRY.md#src-010)

**Engineering lesson:** ít parameter và thậm chí không trainable parameter không đảm bảo latency thấp; operator structure và rollout strategy quan trọng.

---

## 6. TCN, GRU, Mamba — architecture evidence

### TCN

Generic TCN study cho thấy causal/dilated convolution là baseline sequence modeling mạnh [SRC-032](sources/SOURCE_REGISTRY.md#src-032). Trong quadrotor domain, End2End-TCN, PI-TCN và PI-WAN tạo precedent rõ [SRC-004](sources/SOURCE_REGISTRY.md#src-004), [SRC-005](sources/SOURCE_REGISTRY.md#src-005), [SRC-016](sources/SOURCE_REGISTRY.md#src-016).

### GRU/RNN

RNN family có strong precedent trên real dynamic systems [SRC-003](sources/SOURCE_REGISTRY.md#src-003). GRU còn được dùng trong wind estimation trên unseen whole flights [SRC-018](sources/SOURCE_REGISTRY.md#src-018).

### Mamba/SSM

Original Mamba dùng selective state spaces với linear sequence scaling [SRC-014](sources/SOURCE_REGISTRY.md#src-014). Một quadrotor wind-conditioned Mamba study available online 2026 dùng compact two-layer Mamba, sequence length 32 và RotorPy simulation [SRC-015](sources/SOURCE_REGISTRY.md#src-015).

Trong benchmark của study đó:

| Model | Params | Inference reported on RTX A6000 |
|---|---:|---:|
| TCN | 42.8K | 0.932 ms |
| LSTM | 51.9K | 1.747 ms |
| Mamba | 26.9K | 1.224 ms |

[SRC-015](sources/SOURCE_REGISTRY.md#src-015)

**Interpretation:** Mamba ít parameter hơn nhưng không nhanh hơn TCN trong setting sequence=32; study là simulation/RotorPy, không chứng minh embedded real-flight latency. Mamba phải là challenger cho long-context residual memory, không phải mặc định.

---

## 7. Disturbance / wind estimation

### INDI

Cascaded INDI sử dụng measured acceleration/incremental control effectiveness để giảm model dependence và đã chứng minh disturbance rejection trong 10 m/s wind-tunnel exhaust [SRC-028](sources/SOURCE_REGISTRY.md#src-028).

Use trong project là candidate incremental representation:

\[
\Delta \dot X\approx G_t\Delta U+d_t,
\]

không phải lấy INDI làm controller của Pipeline 2.

### HGDO / UIO

HGDO 2024 cung cấp real-time disturbance estimates và laboratory validation [SRC-029](sources/SOURCE_REGISTRY.md#src-029). UIO-based wind-gust estimation 2021 estimate unknown exogenous wind inputs trong quasi-hover formulation [SRC-030](sources/SOURCE_REGISTRY.md#src-030).

Các phương pháp này là candidate feature/observer, không phải default core vì noise/model/rank assumptions.

### Wind-learning evidence

Data rotation/reduction giúp GRU đạt khoảng 0.48 m/s RMSE trên new complete flights trong một wind-estimation study; previous random-sample evaluation có thể đẹp hơn đáng kể [SRC-018](sources/SOURCE_REGISTRY.md#src-018).

Một paper available online 2026 sử dụng 1,750 real free-flight missions và so MLP-attention, GRU, BNN, TabNet cho telemetry-only wind estimation; authors chỉ ra ground-anemometer spatial mismatch là một source error quan trọng [SRC-019](sources/SOURCE_REGISTRY.md#src-019).

**Implication:** wind labels cũng có uncertainty.

---

## 8. Sparse / symbolic dynamics

SINDYc mở rộng sparse identification sang systems có control/external input [SRC-025](sources/SOURCE_REGISTRY.md#src-025):

\[
\dot X=\Theta(X,U)\Xi,
\]

với \(\Xi\) sparse. E-SINDy dùng bagging để tăng robustness với noisy/limited data và tạo inclusion probabilities [SRC-026](sources/SOURCE_REGISTRY.md#src-026). Symbolic regression đã được áp dụng cho quadrotor dynamics [SRC-027](sources/SOURCE_REGISTRY.md#src-027).

**Conclusion:** symbolic discovery là candidate tốt cho explicit nominal core nhưng không thể là novelty chỉ vì “symbolic UAV”. Contribution phải nằm ở việc **hybridize explicit core với compact temporal residual và chứng minh complexity reduction**.

---

## 9. Embedded / real-time evidence

Real-time Neural MPC cho thấy learned neural dynamics có thể được tích hợp vào 50 Hz real-world quadrotor MPC bằng solver/model integration thích hợp [SRC-013](sources/SOURCE_REGISTRY.md#src-013). Tiny LB-MPC 2024 khai thác multirotor structure và solver-aware design để chạy 100 Hz trên 53 g Crazyflie + Teensy 4.0 [SRC-012](sources/SOURCE_REGISTRY.md#src-012).

**Engineering lesson:** real-time feasibility là property của **whole computational graph**, không phải parameter count của neural network.

---

## 10. World models trong aerial robotics hiện đại

MAD 2026 là geometry-aware world model cho visual quadrotor navigation, học recurrent latent dynamics của occupancy/visibility và deploy policy trên physical quadrotor [SRC-034](sources/SOURCE_REGISTRY.md#src-034). Đây là evidence rằng thuật ngữ “world model” trong UAV hiện bao phủ high-level perception/navigation.

Project hiện tại khác scope:

```text
MAD / visual world model:
depth/geometry/history → occupancy/visibility/ego-motion → navigation policy

Project này:
command/state/actuator history → physical actuation/effect → low-level future dynamics
```

Do đó nên dùng thuật ngữ **low-level dynamics Action-to-Effect World Model**.

---

## 11. Synthesis

Literature đã làm tốt:

\[
\text{multi-step}
+
\text{temporal networks}
+
\text{physics residual}
+
\text{wind adaptation}
+
\text{uncertainty models},
\]

nhưng evidence vẫn phân mảnh giữa actuation, physical dynamics, disturbance, compute và reliability.

Research opportunity của project là kiểm tra một structured decomposition trong real-flight setting:

\[
\boxed{
\text{canonical data}
\rightarrow
\text{actuator model}
\rightarrow
\text{explicit nominal dynamics}
+
\text{compact temporal residual}
\rightarrow
\text{multi-horizon state}
+
\text{uncertainty/support}
}
\]

và đánh giá bằng:

\[
\boxed{
\text{accuracy}+
\text{whole-flight generalization}+
\text{latency}+
\text{complexity}+
\text{calibration}
}
\]

Không thành phần nào ở trên được coi là novel riêng lẻ. Novelty candidate là **problem formulation + decomposition + evidence-driven model simplification + deployment-relevant evaluation**, nếu experiment sau này thực sự ủng hộ.
