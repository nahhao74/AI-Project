# 06 — Feasibility and Expected Value

## 1. Feasibility question

Project có khả thi không nếu không sở hữu UAV hardware để thu dữ liệu riêng?

**Kết luận Step 1:** khả thi cho **offline real-flight predictive research**, vì có nhiều public datasets bổ sung cho nhau. Tuy nhiên không dataset đơn lẻ cung cấp hoàn chỉnh command + actual actuator + wind + external-force + full state trong mọi regime.

Do đó strategy phải là **modular evidence**, không merge mù tất cả datasets.

---

## 2. Dataset feasibility

### Pelican

Official dataset có 54 flights và gồm Vicon position/orientation, actual motor speeds, commanded motor speeds, derived velocity/body rates [SRC-020](sources/SOURCE_REGISTRY.md#src-020).

Best role:

\[
U^{cmd}\rightarrow M^{actual}\rightarrow X.
\]

Đây là primary dataset cho actuator decomposition.

### Nano-drone benchmark 2026

Khoảng 75k samples, four aggressive trajectories, synchronized 4-D motor input, 13-D state output, standard 0.5 s multi-step metrics và embedded reference implementations [SRC-010](sources/SOURCE_REGISTRY.md#src-010).

Best role: benchmark reproduction; preprocessing/time alignment; embedded complexity; aggressive dynamics.

### NeuroBEM

1 h 15 min aggressive real flight, 400 Hz, position/velocity/acceleration/pose/body rates/angular acceleration, battery voltage, individual motor speeds [SRC-021](sources/SOURCE_REGISTRY.md#src-021).

Best role: aggressive/aerodynamic residual; high-rate temporal analysis; battery/context features.

### VID

Hardware-synchronized visual/inertial/dynamical data có target RPM, actual RPM/current, control inputs và một số sequences có 6-axis external force ground truth [SRC-022](sources/SOURCE_REGISTRY.md#src-022), [SRC-023](sources/SOURCE_REGISTRY.md#src-023).

Best role: actuator validation; external-force/disturbance supervision; loaded vs loadless regimes.

### AMOVFLY

270+ flights, 46+ h, 3 UAVs, wind speed/direction, multiple altitude/speed/payload scenarios [SRC-024](sources/SOURCE_REGISTRY.md#src-024).

Best role: wind/environment-conditioned evaluation; cross-UAV/context study.

Limitation: ready-data signal set không giàu motor command/actual RPM như Pelican/VID.

---

## 3. Why not merge all datasets directly?

Datasets khác nhau ở coordinate frames, airframe, controller, sample rates, actuator semantics, sensor pipelines, ground-truth source và available variables.

Một tensor chung sẽ silently assume:

\[
U^{(A)}\equiv U^{(B)}
\]

và signal semantics tương đương, điều chưa được chứng minh.

V1 dùng mỗi dataset như một **scientific instrument** cho một hypothesis cụ thể.

---

## 4. Compute feasibility

Các reference cho thấy compact dynamics models có thể phù hợp embedded hardware nếu thiết kế đúng:

- Nano benchmark đo khoảng 1–3 ms/step cho several small models/physics hybrid trên target STM32, tùy model [SRC-010](sources/SOURCE_REGISTRY.md#src-010).
- Tiny learning-based MPC chạy 100 Hz trên 53 g multirotor với Teensy 4.0 nhờ solver-aware/structured design [SRC-012](sources/SOURCE_REGISTRY.md#src-012).
- Real-time Neural MPC demonstrated learned-model MPC at 50 Hz onboard quadrotor bằng integration strategy phù hợp [SRC-013](sources/SOURCE_REGISTRY.md#src-013).

Điều này không đảm bảo proposed model sẽ real-time; nó chỉ chứng minh objective **physically plausible**.

---

## 5. Data-processing feasibility

Nano benchmark đã công bố practical pipeline: clock/source alignment, 100 Hz resampling, cross-correlation motor–acceleration alignment, per-signal filtering, quaternion log/exp processing [SRC-010](sources/SOURCE_REGISTRY.md#src-010).

Điều này cung cấp reference engineering baseline cho Step 2/3.

---

## 6. Symbolic/hybrid feasibility

SINDYc cung cấp controlled nonlinear sparse discovery [SRC-025](sources/SOURCE_REGISTRY.md#src-025). E-SINDy tăng robustness khi noisy/low-data [SRC-026](sources/SOURCE_REGISTRY.md#src-026). Quadrotor symbolic regression đã được demonstrated trong literature [SRC-027](sources/SOURCE_REGISTRY.md#src-027).

Do real flight noisy, V1 không nên dùng numerical differentiation thô làm sole symbolic target. Step 2 sẽ xem xét weak/integral formulation hoặc derivative targets đã quality-controlled.

---

## 7. Expected value — không phải promised result

### EV-01 — Lower neural burden

\[
\text{canonicalization}
+
\text{explicit nominal dynamics}
\Rightarrow
\text{smaller residual network}
\]

là expectation cần test, không phải guarantee.

### EV-02 — Better runtime semantics

Explicit actuator model cho output có interpretation:

\[
\hat M(h),\quad T_{10},T_{50},T_{90}.
\]

### EV-03 — Better generalization diagnosis

Whole-flight split + regime OOD giúp phân biệt interpolation với genuine generalization.

### EV-04 — Better reliability

Uncertainty/support cho phép `SUPPORTED / BOUNDARY / OOD-ABSTAIN` thay vì always-predict.

### EV-05 — Better latency accounting

\[
H_{\text{usable}}
=
H_{\text{accurate}}
-
\tau_{\text{pipeline}}
-
\tau_{\text{effect}}
\]

đưa compute/action delay vào model selection.

---

## 8. Main risks

| Risk | Consequence | Mitigation |
|---|---|---|
| Dataset semantics inconsistent | false cross-dataset conclusions | dataset-specific contracts |
| No future command in some datasets | cannot test deployable planning input | use Pelican/VID first |
| Symbolic derivative noise | unstable equations | weak/integral/ensemble methods |
| Mamba runtime operators poorly optimized | higher actual latency | measure, do not infer from params |
| Wind GT spatial mismatch | label noise | model label uncertainty; no perfect-GT assumption |
| Public datasets too narrow | weak OOD claims | use multiple independent datasets |
| Over-large hybrid pipeline | defeats low-latency goal | Pareto + ablation pruning |
| No owned hardware | no onboard claim | scope claims to offline real-flight validity |

---

## 9. Feasibility decision

```text
REAL_FLIGHT_OFFLINE_RESEARCH_FEASIBLE  = true
ACTUATOR_DECOMPOSITION_FEASIBLE        = true (Pelican/VID)
STANDARD_BENCHMARK_FEASIBLE            = true (Nano)
WIND_CONTEXT_FEASIBLE                  = true (AMOVFLY/other wind datasets)
EXTERNAL_FORCE_FEASIBLE                = true (VID)
EMBEDDED_REALTIME_CLAIM_FEASIBLE_NOW   = false
CLOSED_LOOP_CONTROL_CLAIM_FEASIBLE_NOW = false
```

Step 2 phải chuyển feasibility này thành exact dataset contracts và experiment ladder.
