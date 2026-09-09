# 08 — Extended Research Synthesis: Reduced-Order, Operator and Structure-Preserving Directions

**Status:** `FOUNDATION_EXTENSION_V1.1`  
**Evidence cutoff:** 2026-09-09  
**Purpose:** bổ sung các hướng nghiên cứu xuất hiện sau khi Step 1 V1 đã khóa, nhưng không thay đổi problem formulation cốt lõi.

## 1. Vì sao cần bổ sung

Step 1 V1 đã xác lập rằng novelty không thể chỉ là `TCN`, `Mamba`, `physics-informed`, `wind-aware` hay `symbolic`. Vòng research tiếp theo cho thấy một câu hỏi còn sâu hơn:

> Có thể giảm độ khó của World Model bằng cách xác định **dynamic order**, tách **low-rank temporal structure**, giữ **analytic structure** hữu ích và chỉ giao phần còn lại cho neural residual hay không?

Điều này mở rộng decomposition hiện tại theo hướng:

```text
real-flight data
→ valid/canonical representation
→ low-rank / system-order discovery
→ explicit or lifted dynamics
→ compact learned residual
→ calibrated reliability
```

## 2. Subspace identification và dynamic order

N4SID sử dụng projection, QR và SVD để identify state sequence và state-space matrices từ MIMO input/output data. Original formulation là non-iterative và numerically stable [ER-SRC-001].

Một mục tiêu quan trọng của project không phải chỉ là fit `A,B,C,D`, mà là ước lượng **effective dynamic order** trước khi chọn latent dimension của neural/Koopman model.

Với block-Hankel matrix $H$:

```math
H=U\Sigma V^\top,
```

singular-value spectrum cung cấp evidence về số mode động lực học có support đáng kể trong dữ liệu.

**Implication:** các giá trị như `latent_dim=32`, `Mamba state=16`, `VARX order=8` không được chọn chỉ theo precedent; chúng cần được so với data-supported dynamic order.

## 3. Errors-in-Variables và Total Least Squares

Real-flight identification không thỏa tốt giả định rằng regressors là noise-free. Motor measurement, IMU, derived velocity/body-rate và aligned actuator signals đều có uncertainty.

Classical Errors-in-Variables (EIV) literature chỉ ra rằng khi noise tồn tại ở cả input và output, ordinary least squares có thể bị biased; Total Least Squares là một family phù hợp hơn cho overdetermined systems khi cả coefficient matrix và right-hand side bị perturb [ER-SRC-002]. Recent recursive nonlinear EIV identification tiếp tục xem đây là vấn đề thực tế của dynamic parameter estimation [ER-SRC-003].

**Implication:** `VARX-OLS` phải là baseline, không phải authority. `VARX-Ridge`, `TLS/weighted-TLS` là challengers để bảo đảm neural model không được so với một classical baseline yếu do sai noise assumptions.

## 4. Koopman / lifted linear dynamics

Koopman-style modeling tìm observables $z=\phi(x)$ sao cho dynamics trong lifted space gần tuyến tính:

```math
z_{t+1}=Az_t+Bu_t.
```

Giá trị của hướng này đối với project là **cheap rollout after lifting** và compatibility với reduced-order analysis. Tuy nhiên naive Koopman learning có thể tạo operator không ổn định; vì vậy stability/stabilizability-aware Koopman variants đáng được coi là challenger thay vì chỉ EDMD cơ bản.

Conformal Koopman 2026 tiếp tục cho thấy Koopman embeddings có thể kết hợp với statistically calibrated model-error bounds và được kiểm chứng trên real-world nonlinear aerial platform [ER-SRC-004].

**Implication:** Koopman không được mặc định chọn, nhưng là một bridge hợp lý giữa linear system identification và nonlinear neural World Model.

## 5. Flatness-preserving residual learning

Generic learned residual có thể phá analytic structure hữu ích của nominal system. Flatness-preserving residual learning 2025–2026 đặt constraint lên residual để giữ differential flatness; hardware quadrotor formation work 2026 báo cáo tracking improvement đồng thời giữ loop rate 5 ms và computation thấp hơn NMPC trong setup của họ [ER-SRC-005], [ER-SRC-006].

Điểm project học từ hướng này không phải là copy formation controller, mà là principle:

```math
\text{learned correction}\neq\text{arbitrary correction}.
```

Nếu một explicit model có structure hữu ích cho downstream planning/control, residual architecture phải được kiểm tra xem có phá structure đó không.

## 6. Reservoir computing / Echo State Network

Echo State Networks sử dụng fixed recurrent reservoir và train chủ yếu linear readout, thường bằng ridge regression. Recent 2026 research tiếp tục dùng reservoir computing cho system identification và MPC vì surrogate cần vừa accurate vừa computationally cheap [ER-SRC-007]. Reduced-order ESN work 2026 còn cho thấy model reduction có thể giảm optimization burden mạnh trong MPC settings [ER-SRC-008].

**Implication:** ESN/NGRC là challenger hợp lý trong temporal residual experiment, đặc biệt khi training cost và runtime simplicity quan trọng. Chưa có đủ UAV real-flight evidence để promote thành primary backbone.

## 7. Online adaptation là V2, không phải V1

Real plant có thể drift theo battery, payload, motor aging hoặc environment. Real-quadrotor work 2026 cho thấy low-rank online neural adaptation có thể cập nhật dynamics model nhanh mà không retrain toàn network [ER-SRC-009].

V1 vẫn ưu tiên frozen offline model để scientific attribution rõ. V2 mới xem xét:

```text
prediction residual
→ change detector
→ RLS / low-rank adaptation
```

## 8. Kết luận đối với novelty

Sau vòng research mở rộng, novelty candidate càng không nằm ở một architecture đơn lẻ. Scientific contribution chỉ có giá trị nếu experiments chứng minh được chuỗi logic:

```math
\boxed{
\text{better structure discovery}
\rightarrow
\text{smaller/easier prediction problem}
\rightarrow
\text{lower neural burden}
\rightarrow
\text{better accuracy--latency--reliability trade-off}
}
```

Các hướng mới được đưa vào **candidate set**, không được coi là selected mechanisms.

---

## Extended sources

### ER-SRC-001 — N4SID
P. Van Overschee, B. De Moor, **“N4SID: Subspace algorithms for the identification of combined deterministic-stochastic systems,”** *Automatica*, 1994.  
https://doi.org/10.1016/0005-1098(94)90230-5

### ER-SRC-002 — Errors-in-Variables survey
T. Söderström, **“Errors-in-variables methods in system identification,”** *Automatica*, 2007.  
https://www.sciencedirect.com/science/article/pii/S0005109807000714

### ER-SRC-003 — Nonlinear recursive EIV identification
H. Koide, J. Vayssettes, G. Mercère, **“A recursive parameter identification algorithm for nonlinear errors-in-variables models,”** *IFAC Journal of Systems and Control*, 2026.  
https://doi.org/10.1016/j.ifacsc.2026.100381

### ER-SRC-004 — Conformal Koopman
K. Hirano, H. Tsukamoto, **“Conformal Koopman for Embedded Nonlinear Control with Statistical Robustness: Theory and Real-World Validation,”** preprint, 2026.  
https://arxiv.org/abs/2603.21580

### ER-SRC-005 — Flatness-preserving residuals
F. Yang, J. Welde, N. Matni, **“Learning Flatness-Preserving Residuals for Pure-Feedback Systems,”** preprint, 2025.  
https://arxiv.org/abs/2504.04324

### ER-SRC-006 — Hardware flatness-preserving quadrotor residual learning
P.-A. Hsieh, F. Yang, N. Matni, M. A. Hsieh, **“Flatness-Preserving Residual Learning for Real-Time Tight Quadrotor Formation Flight,”** preprint, 2026.  
https://arxiv.org/abs/2607.12275

### ER-SRC-007 — Reservoir computing for SysID/MPC
J. P. Williams, J. N. Kutz, K. Manohar, **“Reservoir computing for system identification and model predictive control,”** *Neural Networks*, available online 2026.  
https://doi.org/10.1016/j.neunet.2026.109031

### ER-SRC-008 — Reduced-order Echo State Networks
**“Reduced-order echo state networks for model predictive control in industrial applications,”** *European Journal of Control*, 2026.  
https://doi.org/10.1016/j.ejcon.2026.101527

### ER-SRC-009 — Online low-rank robot dynamics adaptation
A. Altawaitan, N. Atanasov, **“Adapting Neural Robot Dynamics on the Fly for Predictive Control,”** preprint, 2026.  
https://arxiv.org/abs/2604.04039
