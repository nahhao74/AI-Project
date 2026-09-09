# 01 — Problem Definition

## 1. Engineering context

Một UAV dynamics World Model dùng cho planning/control phải trả lời câu hỏi mạnh hơn “state tiếp theo là gì?”. Với action $U$, điều downstream controller thực sự cần là:

```math
\text{What physical effect will this command cause, when will it occur, and how reliable is the prediction?}
```

World-model literature cho Physical AI định nghĩa world model như một learned predictive representation phục vụ decision-making, đồng thời chỉ ra rằng long-horizon error, uncertainty, planner exploitation và real-time constraints là các vấn đề deployment quan trọng [SRC-001](sources/SOURCE_REGISTRY.md#src-001).

Trong phạm vi project này, bài toán được giới hạn ở **low-level multirotor physical dynamics trên dữ liệu bay thật**.

## 2. Conventional formulation và limitation

Một learned dynamics model thường có dạng:

```math
\hat X_{t+1:t+H}
=
F_\theta
\left(
X_{t-L:t},
U_{t:t+H-1}
\right).
```

Formulation này đã được nghiên cứu nhiều năm. Pelican/RNN work đã làm multi-step flight prediction trên dữ liệu thật [SRC-002](sources/SOURCE_REGISTRY.md#src-002), [SRC-003](sources/SOURCE_REGISTRY.md#src-003); End2End-TCN đưa future control sequence vào temporal network [SRC-004](sources/SOURCE_REGISTRY.md#src-004); Rao et al. phân tích architecture/history/multi-step loss và decoupled prediction [SRC-007](sources/SOURCE_REGISTRY.md#src-007).

Vì vậy vấn đề không phải thiếu một function approximator cho $F$. Vấn đề là **representation và causal/physical interface của $F$**.

## 3. Problem P1 — Command không đồng nghĩa physical actuation

Một planner biết command sẽ gửi:

```math
U^{cmd}_{t:t+H}
```

nhưng physical plant chịu tác động qua actuator:

```math
U^{cmd}
\rightarrow
\text{ESC/motor/propeller}
\rightarrow
M^{actual}
\rightarrow
F,\tau
\rightarrow
X.
```

Eschmann et al. chỉ ra motor delays thường bị bỏ qua dù quan trọng đối với end-to-end control, và đề xuất data-driven identification của first-order motor delay từ proprioceptive flight data [SRC-009](sources/SOURCE_REGISTRY.md#src-009).

Nano-drone benchmark 2026 còn thực hiện explicit motor–acceleration temporal alignment bằng cross-correlation và mô tả actuation nonlinearities như một phần làm identification khó [SRC-010](sources/SOURCE_REGISTRY.md#src-010).

Do đó assumption:

```math
U^{cmd}(t)\equiv U^{physical}(t)
```

không được xem là mặc định hợp lệ.

### Proposed scientific problem

Có lợi không nếu factorize:

```math
\boxed{
U^{cmd}
\rightarrow
\hat M^{actual}
\rightarrow
\hat X_{future}
}
```

thay vì direct:

```math
U^{cmd}\rightarrow\hat X_{future}?
```

## 4. Problem P2 — Model có thể phí capacity để học invariance do representation kém

Raw logs có thể chứa:

- absolute world-frame position;
- quaternion với double-cover $q\equiv -q$;
- different ENU/NED/body conventions;
- yaw-dependent translational coordinates;
- motor signals có scale/correlation khác nhau.

Nano-drone benchmark không filter quaternion trực tiếp trong $\mathbb R^4$; họ dùng logarithmic map sang rotation-vector, filter trong linear tangent representation rồi map ngược bằng exponential map để bảo toàn cấu trúc $SO(3)$ [SRC-010](sources/SOURCE_REGISTRY.md#src-010).

Wind-estimation experiments cũng cho thấy evaluation bằng random samples có thể đánh giá generalization quá lạc quan; data rotation/reduction giúp cải thiện performance trên complete unseen flights [SRC-018](sources/SOURCE_REGISTRY.md#src-018).

### Proposed scientific problem

Có thể chuyển một phần complexity khỏi neural network bằng:

```math
\text{raw coordinates}
\rightarrow
\text{geometry-canonical representation}
```

và đạt:

```math
E_{\text{small canonical model}}
\le
E_{\text{larger raw model}}?
```

## 5. Problem P3 — Pure neural model có thể học lại dynamics đơn giản

Hybrid dynamics learning đã repeatedly cho thấy việc kết hợp structure và learning có lợi [SRC-003](sources/SOURCE_REGISTRY.md#src-003), [SRC-006](sources/SOURCE_REGISTRY.md#src-006), [SRC-005](sources/SOURCE_REGISTRY.md#src-005).

Sparse system discovery như SINDYc có khả năng tìm governing equations với control inputs [SRC-025](sources/SOURCE_REGISTRY.md#src-025); Ensemble-SINDy tăng robustness ở low-data/high-noise settings [SRC-026](sources/SOURCE_REGISTRY.md#src-026). Symbolic regression cũng đã được áp dụng cho quadrotor dynamics, nghĩa là “dùng symbolic cho UAV” tự nó không còn là novelty [SRC-027](sources/SOURCE_REGISTRY.md#src-027).

Vấn đề cần kiểm tra là:

```math
\dot X
=
f_{\text{explicit}}(X,U)
+
r_\theta(H_t).
```

Nếu $f_{\text{explicit}}$ giải thích đủ phần nominal dynamics, neural network chỉ cần học residual có thể nhỏ hơn và rẻ hơn.

## 6. Problem P4 — Long-horizon accuracy và compute xung đột

Long-horizon prediction có compounding error [SRC-007](sources/SOURCE_REGISTRY.md#src-007). Recursive rollout cũng làm compute tăng theo số bước.

Nano-drone benchmark đo embedded inference trên STM32: trong setup của họ, Res-MLP khoảng 1.03 ms/step, Res-LSTM khoảng 2.09 ms/step, physics model khoảng 1.79 ms/step và hybrid khoảng 2.82 ms/step; N-step rollout vì vậy scale theo $N T^{inf}$ [SRC-010](sources/SOURCE_REGISTRY.md#src-010). Kết quả quan trọng là **zero trainable parameters không đồng nghĩa compute thấp**, vì rigid-body operations cũng có cost.

Một wind-conditioned Mamba study 2026 dùng sequence length 32 cho thấy base Mamba ít parameters hơn TCN (26.9K vs 42.8K) nhưng inference được báo cáo chậm hơn trên RTX A6000 (1.224 vs 0.932 ms). Đây là simulation-oriented study, không phải embedded benchmark [SRC-015](sources/SOURCE_REGISTRY.md#src-015).

Do đó:

```math
\text{parameter count}\not\Rightarrow\text{runtime latency}.
```

### Proposed scientific problem

Architecture selection phải dựa trên Pareto:

```math
\min
\{
E(H),\;
T_{p99},\;
Memory,\;
MACs
\}
```

thay vì chọn backbone mới nhất.

## 7. Problem P5 — Disturbance semantics bị trộn lẫn

External deviation có thể đến từ:

```math
d=
d_{wind}
+
d_{aero}
+
d_{payload}
+
d_{actuator}
+
d_{model}
+
d_{sensor}.
```

VID cung cấp explicit 6-axis external-force ground truth trong một số sequence và target/actual motor RPM [SRC-022](sources/SOURCE_REGISTRY.md#src-022), [SRC-023](sources/SOURCE_REGISTRY.md#src-023). AMOVFLY cung cấp hơn 270 flights/46 h cùng wind speed/direction nhưng không có cùng actuator richness như VID/Pelican [SRC-024](sources/SOURCE_REGISTRY.md#src-024).

Vì vậy:

```math
\text{unknown residual}\neq \text{wind}
```

trừ khi có supervision/physical assumptions đủ mạnh.

## 8. Problem P6 — Prediction không có reliability boundary

Deterministic model vẫn trả output ở OOD states. World-model survey 2026 nhấn mạnh distribution shift, model bias và planner exploitation [SRC-001](sources/SOURCE_REGISTRY.md#src-001). DroneDiffusion nhắm tới multimodal uncertainty trong real quadrotor dynamics [SRC-008](sources/SOURCE_REGISTRY.md#src-008); UL-MPC 2026 dùng probabilistic residual mean/covariance nhưng hiện evidence được công bố là high-fidelity simulation [SRC-017](sources/SOURCE_REGISTRY.md#src-017).

Project cần tách:

```math
\text{prediction}
```

khỏi:

```math
\text{prediction is supported/reliable}.
```

## 9. Problem P7 — Evaluation split có thể tạo kết quả giả lạc quan

Wind-estimation work báo cáo LSTM khoảng 0.34 m/s RMSE trên unseen randomly selected samples nhưng khoảng 0.55 m/s trên complete new flights; work tiếp theo dùng data rotation/reduction và GRU đạt khoảng 0.48 m/s trên new flights [SRC-018](sources/SOURCE_REGISTRY.md#src-018).

Do temporal windows chồng lấn mạnh, random-window split có nguy cơ leakage gần-trùng.

### Requirement

Primary scientific evaluation phải dùng:

```math
\boxed{\text{whole-flight / whole-trajectory held-out}}
```

và thêm regime-OOD khi data cho phép.

## 10. Problem statement tổng hợp

Project sẽ kiểm tra liệu một **real-flight Action-to-Effect World Model** có thể đạt trade-off tốt hơn bằng cách:

1. canonicalize geometry/time trước learning;
2. tách command khỏi actual actuation;
3. giải phần nominal/simple dynamics bằng explicit/sparse model khi evidence cho phép;
4. giao residual temporal dynamics cho một compact sequence model được chọn bằng ablation;
5. dự đoán trực tiếp multi-horizon outputs;
6. định lượng uncertainty/support;
7. đánh giá cả prediction quality lẫn usable latency.

Formulation làm việc:

```math
\begin{aligned}
\hat M_{t:t+H} &= A_\phi(H_t,U^{cmd}_{t:t+H}),\\
\hat f^{nom} &= G_\psi(X_t,\hat M_t),\\
\hat r_{t:t+H} &= R_\theta(H_t,\hat M_{t:t+H}),\\
\hat X_{t:t+H} &= \mathcal{I}(\hat f^{nom}+\hat r),\\
(\Sigma,S) &= Q_\eta(H_t,\hat X).
\end{aligned}
```

Các hàm $A,G,R,Q$ **chưa được freeze architecture** trong Step 1.
