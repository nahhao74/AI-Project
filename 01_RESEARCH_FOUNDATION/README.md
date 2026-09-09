# Step 1 — Research Foundation

**Research status:** `COMPLETE_V1`  
**Evidence cutoff:** 2026-09-09  
**Primary domain:** real-flight multirotor dynamics / learned world models / system identification / edge-aware prediction

## 1. Mục đích

Step 1 thiết lập cơ sở khoa học cho toàn project trước khi lựa chọn architecture. Mục tiêu không phải chứng minh trước rằng một thiết kế cụ thể như TCN, Mamba, SINDy hay whitening là tốt; mục tiêu là xác định:

1. vấn đề kỹ thuật nào đã được literature giải tốt;
2. vấn đề nào vẫn còn hạn chế;
3. limitation nào quan trọng đối với một World Model dự kiến dùng cho control;
4. public real-flight data nào đủ để kiểm tra từng giả thuyết;
5. mechanism nào có lý do khoa học để trở thành candidate;
6. experiment nào sau này có quyền xác nhận hoặc loại candidate đó.

## 2. Thesis logic

Literature hiện cho thấy bốn kết luận nền:

### 2.1 Dự đoán multi-step không còn là novelty

RNN/hybrid models đã được dùng để dự đoán quadrotor nhiều bước trên dữ liệu thực từ ít nhất 2018–2019 [SRC-002](sources/SOURCE_REGISTRY.md#src-002), [SRC-003](sources/SOURCE_REGISTRY.md#src-003). TCN, physics-inspired temporal networks và long-horizon decoupled models tiếp tục cải thiện vấn đề compounding error [SRC-004](sources/SOURCE_REGISTRY.md#src-004), [SRC-005](sources/SOURCE_REGISTRY.md#src-005), [SRC-007](sources/SOURCE_REGISTRY.md#src-007).

Vì vậy project không được claim novelty từ:

```math
(X,U)\rightarrow X_{future}
```

hoặc chỉ từ việc đổi RNN sang TCN/Mamba.

### 2.2 Hybrid physics + learning cũng đã được chứng minh mạnh

NeuroBEM và nhiều công trình sau cho thấy nominal/physics structure kết hợp learned residual có thể cải thiện prediction/generalization [SRC-006](sources/SOURCE_REGISTRY.md#src-006). PI-TCN và PI-WAN đưa physical constraints vào temporal networks [SRC-005](sources/SOURCE_REGISTRY.md#src-005), [SRC-016](sources/SOURCE_REGISTRY.md#src-016). Do đó `physics + NN` không phải contribution đủ riêng.

### 2.3 Actuation và timing vẫn là failure mode thực tế

System identification trên quadrotor chỉ ra motor delay thường bị bỏ qua dù quan trọng cho end-to-end control [SRC-009](sources/SOURCE_REGISTRY.md#src-009). Nano-drone benchmark 2026 tiếp tục cho thấy real-world system identification chịu ảnh hưởng của actuation nonlinearities, cần alignment command/motor/acceleration, và chi phí embedded prediction không thể bỏ qua [SRC-010](sources/SOURCE_REGISTRY.md#src-010).

Đây là nền tảng cho formulation:

```math
U^{cmd}\rightarrow M^{actual}\rightarrow X_{future}
```

thay vì coi $U^{cmd}$ là tác động vật lý tức thời.

### 2.4 Reliability và compute là một phần của World Model dùng cho Physical AI

Survey Physical AI 2026 nhấn mạnh compounding error, uncertainty calibration, planner exploitation, distribution shift và real-time constraints là các tension trung tâm của world-model deployment [SRC-001](sources/SOURCE_REGISTRY.md#src-001). Tiny learning-based MPC đã chứng minh cấu trúc model/solver phải được thiết kế theo embedded budget để đạt 100 Hz trên tiny multirotor [SRC-012](sources/SOURCE_REGISTRY.md#src-012).

Do đó metric trung tâm không thể chỉ là RMSE.

## 3. Research thesis hiện tại

Project nghiên cứu giả thuyết tổng quát:

> **Một representation được canonicalize theo hình học, kết hợp explicit actuation-to-effect decomposition và một nominal/symbolic dynamics core rẻ, có thể giảm learning burden của temporal neural model; nếu residual model được chọn theo accuracy–latency Pareto và đi kèm uncertainty/support estimation, hệ thống có thể đạt prediction hữu ích hơn cho control so với pure neural state forecasting có complexity tương đương.**

Đây là **hypothesis**, chưa phải conclusion.

## 4. Không freeze architecture trong Step 1

Các candidate hiện tại:

- MIMO VARX;
- SINDYc / weak or ensemble SINDy;
- INDI-inspired incremental features;
- causal TCN;
- GRU;
- Mamba/SSM;
- HGDO/UIO-derived disturbance features;
- blockwise whitening;
- Mahalanobis support;
- probabilistic output.

Tất cả có trạng thái:

```text
PROPOSED_FOR_EVALUATION
```

## 5. Evidence hierarchy

Ưu tiên nguồn theo thứ tự:

1. peer-reviewed journal/conference;
2. official dataset/benchmark publication;
3. official code/dataset repository;
4. current survey/review;
5. arXiv preprint khi chưa có bản peer-reviewed hoặc để theo dõi frontier.

Các paper có issue date sau ngày evidence cutoff nhưng đã available-online được đánh dấu rõ trong Source Registry; không dùng chúng để tạo claim “đã được real-flight validated” nếu paper chỉ simulation.

## 6. Traceability

| Research item | Tài liệu authority |
|---|---|
| Problem | `01_PROBLEM_DEFINITION.md` |
| Literature | `02_LITERATURE_LANDSCAPE.md` |
| Gaps | `03_RESEARCH_GAP_ANALYSIS.md` |
| Hypotheses | `04_RESEARCH_QUESTIONS_AND_HYPOTHESES.md` |
| Scope | `05_OBJECTIVES_SCOPE_AND_NON_CLAIMS.md` |
| Feasibility | `06_FEASIBILITY_AND_EXPECTED_VALUE.md` |
| Candidate contributions | `07_BASELINE_AND_PROPOSED_CONTRIBUTIONS.md` |
| Source identity | `sources/SOURCE_REGISTRY.md` |
| Cross-paper comparison | `comparison/MODEL_COMPARISON_MATRIX.md` |
| Data feasibility | `comparison/DATASET_COMPARISON_MATRIX.md` |
| Gap→test mapping | `comparison/GAP_TO_CONTRIBUTION_MATRIX.md` |
