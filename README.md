# AI-Project — Real-Flight Action-to-Effect World Modeling

Repository này lưu hồ sơ nghiên cứu và thực nghiệm cho một **World Model động lực học UAV dựa trên dữ liệu bay thật**, với trọng tâm không chỉ là dự đoán trạng thái tương lai mà còn mô hình hóa **đường truyền từ lệnh điều khiển đến tác động vật lý**, độ trễ, phần động lực học có thể mô tả tường minh, phần residual cần học bằng AI, và độ tin cậy của dự đoán.

## Trạng thái hiện tại

```text
PROJECT_STATE                         = STEP_1_RESEARCH_FOUNDATION_COMPLETE
STEP_1_RESEARCH_FOUNDATION           = COMPLETE_V1
STEP_2_SYSTEM_METHODOLOGY            = NOT_STARTED
STEP_3_TECHNICAL_MODEL_DESIGN        = NOT_STARTED
STEP_4_VERIFICATION_AND_RESULTS       = NOT_STARTED

WORLD_MODEL_ARCHITECTURE_SELECTED    = false
TEMPORAL_BACKBONE_SELECTED           = false
TCN_SELECTED                         = false
MAMBA_SELECTED                       = false
SYMBOLIC_CORE_SELECTED               = false
DISTURBANCE_OBSERVER_SELECTED        = false
WHITENING_SELECTED                   = false
```

Các thuật toán đang xuất hiện trong tài liệu Step 1 chỉ là **evidence-backed candidates**. Không thuật toán nào được xem là thiết kế cuối cho tới khi data audit, baseline reproduction và ablation ở các bước sau tạo đủ bằng chứng.

## Research formulation hiện tại

Một World Model cho Physical AI có thể được hiểu là mô hình dự đoán sử dụng lịch sử quan sát và hành động để dự đoán trạng thái, quan sát hoặc đại lượng đủ hữu ích cho ra quyết định [SRC-001](01_RESEARCH_FOUNDATION/sources/SOURCE_REGISTRY.md#src-001). Trong phạm vi project này, formulation được thu hẹp xuống low-level UAV dynamics:

```math
\mathcal{M}_\theta:
(H_t,\;U^{cmd}_{t:t+H})
\mapsto
(\hat X_{t+1:t+H},\;\hat M_{t+1:t+H},\;\Sigma,\;S)
```

với:

- $H_t$: lịch sử causal của state, IMU, actuator và command;
- $U^{cmd}$: command hiện tại/tương lai nếu downstream planner biết trước;
- $\hat M$: diễn tiến actuator thực tế được dự đoán;
- $\hat X$: trạng thái UAV tương lai;
- $\Sigma$: uncertainty được hiệu chuẩn;
- $S$: support/OOD indicator.

Project **không mặc định** rằng `command = physical actuation`, không mặc định rằng mọi residual là gió, và không mặc định rằng neural network phải học toàn bộ dynamics.

## Tài liệu Step 1

- [Research Foundation Overview](01_RESEARCH_FOUNDATION/README.md)
- [Problem Definition](01_RESEARCH_FOUNDATION/01_PROBLEM_DEFINITION.md)
- [Literature Landscape](01_RESEARCH_FOUNDATION/02_LITERATURE_LANDSCAPE.md)
- [Research Gap Analysis](01_RESEARCH_FOUNDATION/03_RESEARCH_GAP_ANALYSIS.md)
- [Research Questions and Hypotheses](01_RESEARCH_FOUNDATION/04_RESEARCH_QUESTIONS_AND_HYPOTHESES.md)
- [Objectives, Scope and Non-Claims](01_RESEARCH_FOUNDATION/05_OBJECTIVES_SCOPE_AND_NON_CLAIMS.md)
- [Feasibility and Expected Value](01_RESEARCH_FOUNDATION/06_FEASIBILITY_AND_EXPECTED_VALUE.md)
- [Baseline and Proposed Contributions](01_RESEARCH_FOUNDATION/07_BASELINE_AND_PROPOSED_CONTRIBUTIONS.md)
- [Source Registry](01_RESEARCH_FOUNDATION/sources/SOURCE_REGISTRY.md)
- [Paper Review Notes](01_RESEARCH_FOUNDATION/sources/PAPER_REVIEW_NOTES.md)
- [Dataset Source Notes](01_RESEARCH_FOUNDATION/sources/DATASET_SOURCE_NOTES.md)
- [Model Comparison Matrix](01_RESEARCH_FOUNDATION/comparison/MODEL_COMPARISON_MATRIX.md)
- [Dataset Comparison Matrix](01_RESEARCH_FOUNDATION/comparison/DATASET_COMPARISON_MATRIX.md)
- [Gap-to-Contribution Matrix](01_RESEARCH_FOUNDATION/comparison/GAP_TO_CONTRIBUTION_MATRIX.md)

## Nguyên tắc nghiên cứu

```text
Problem
  ↓
Evidence
  ↓
Research Gap
  ↓
Falsifiable Hypothesis
  ↓
Candidate Mechanism
  ↓
Controlled Experiment / Ablation
  ↓
Result
  ↓
Engineering Decision
  ↓
Supported Claim
```

Repository này không dùng số lượng thuật toán làm thước đo chất lượng. Một mechanism chỉ được giữ nếu nó cải thiện một failure mode xác định và improvement đủ lớn so với chi phí về latency, memory, complexity hoặc assumptions.
