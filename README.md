# AI-Project — Real-Flight Action-to-Effect World Modeling

Repository này lưu hồ sơ nghiên cứu và thực nghiệm cho một **World Model động lực học UAV dựa trên dữ liệu bay thật**, với trọng tâm không chỉ là dự đoán trạng thái tương lai mà còn mô hình hóa **đường truyền từ lệnh điều khiển đến tác động vật lý**, độ trễ, phần động lực học có thể mô tả tường minh/reduced-order, phần residual cần học bằng AI, và độ tin cậy của dự đoán.

## Trạng thái hiện tại

```text
PROJECT_STATE                         = READY_FOR_EXP_000_SOURCE_DATA_AUDIT
STEP_1_RESEARCH_FOUNDATION           = COMPLETE_V1.1
STEP_2_SYSTEM_METHODOLOGY            = COMPLETE_V1.1
STEP_3_TECHNICAL_MODEL_DESIGN        = NOT_STARTED_ENTRY_CRITERIA_DEFINED
STEP_4_VERIFICATION_AND_RESULTS       = NOT_STARTED

EXP_000_SOURCE_SCHEMA_AUDIT          = NOT_EXECUTED
EXP_010_BASELINE_REPRODUCTION        = NOT_EXECUTED

WORLD_MODEL_ARCHITECTURE_SELECTED    = false
TEMPORAL_BACKBONE_SELECTED           = false
TCN_SELECTED                         = false
MAMBA_SELECTED                       = false
ESN_SELECTED                         = false
KOOPMAN_SELECTED                     = false
SYMBOLIC_CORE_SELECTED               = false
DISTURBANCE_OBSERVER_SELECTED        = false
WHITENING_SELECTED                   = false
LOW_RANK_DENOISING_SELECTED          = false
```

Step 1 xác định **tại sao** project đáng nghiên cứu. Step 2 xác định **cách kiểm tra và quyết định** mà không làm rò rỉ tương lai, không đánh đồng command với physical actuation và không chọn thuật toán theo xu hướng. Step 3 đã có entry contract nhưng chưa bắt đầu freeze technical design; việc đó chỉ được thực hiện sau empirical gates từ EXP-000/010 và các audit representation/timing phù hợp.

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

Project **không mặc định** rằng `command = physical actuation`, không mặc định rằng mọi residual là gió, không mặc định rằng neural network phải học toàn bộ dynamics, và không mặc định rằng latent/model order phải được chọn từ precedent thay vì từ data.

## Tài liệu Step 1 — Research Foundation

- [Research Foundation Overview](01_RESEARCH_FOUNDATION/README.md)
- [Problem Definition](01_RESEARCH_FOUNDATION/01_PROBLEM_DEFINITION.md)
- [Literature Landscape](01_RESEARCH_FOUNDATION/02_LITERATURE_LANDSCAPE.md)
- [Research Gap Analysis](01_RESEARCH_FOUNDATION/03_RESEARCH_GAP_ANALYSIS.md)
- [Research Questions and Hypotheses](01_RESEARCH_FOUNDATION/04_RESEARCH_QUESTIONS_AND_HYPOTHESES.md)
- [Objectives, Scope and Non-Claims](01_RESEARCH_FOUNDATION/05_OBJECTIVES_SCOPE_AND_NON_CLAIMS.md)
- [Feasibility and Expected Value](01_RESEARCH_FOUNDATION/06_FEASIBILITY_AND_EXPECTED_VALUE.md)
- [Baseline and Proposed Contributions](01_RESEARCH_FOUNDATION/07_BASELINE_AND_PROPOSED_CONTRIBUTIONS.md)
- [Extended Reduced-Order / Operator Research Synthesis](01_RESEARCH_FOUNDATION/08_EXTENDED_RESEARCH_SYNTHESIS.md)
- [Source Registry](01_RESEARCH_FOUNDATION/sources/SOURCE_REGISTRY.md)

## Tài liệu Step 2 — System Methodology

- [System Methodology Overview](02_SYSTEM_METHODOLOGY/README.md)
- [System Overview](02_SYSTEM_METHODOLOGY/01_SYSTEM_OVERVIEW.md)
- [Requirements and Constraints](02_SYSTEM_METHODOLOGY/02_REQUIREMENTS_AND_CONSTRAINTS.md)
- [Module Decomposition](02_SYSTEM_METHODOLOGY/03_MODULE_DECOMPOSITION.md)
- [Data and Information Flow](02_SYSTEM_METHODOLOGY/04_DATA_FLOW_AND_INFORMATION_FLOW.md)
- [Latency and Compute Budget](02_SYSTEM_METHODOLOGY/05_LATENCY_AND_COMPUTE_BUDGET.md)
- [Experimental Strategy](02_SYSTEM_METHODOLOGY/06_EXPERIMENTAL_STRATEGY.md)
- [Ablation Strategy](02_SYSTEM_METHODOLOGY/07_ABLATION_STRATEGY.md)
- [Validation and Acceptance Plan](02_SYSTEM_METHODOLOGY/08_VALIDATION_AND_ACCEPTANCE_PLAN.md)
- [Algorithm Placement and Activation Policy](02_SYSTEM_METHODOLOGY/09_ALGORITHM_PLACEMENT_AND_ACTIVATION_POLICY.md)
- [Data Contract](02_SYSTEM_METHODOLOGY/data_strategy/DATA_CONTRACT.md)
- [Dataset Selection Plan](02_SYSTEM_METHODOLOGY/data_strategy/DATASET_SELECTION_PLAN.md)
- [Train/Validation/Test Policy](02_SYSTEM_METHODOLOGY/data_strategy/TRAIN_VALIDATION_TEST_POLICY.md)
- [Preprocessing Strategy](02_SYSTEM_METHODOLOGY/preprocessing_strategy/README.md)
- [Structured Low-Rank and Time-Frequency Plan](02_SYSTEM_METHODOLOGY/preprocessing_strategy/STRUCTURED_LOW_RANK_AND_TIME_FREQUENCY_PLAN.md)
- [Model Selection and Hybrid Plan](02_SYSTEM_METHODOLOGY/modeling_strategy/MODEL_SELECTION_AND_HYBRID_PLAN.md)
- [Reduced-Order, Operator and Reservoir Plan](02_SYSTEM_METHODOLOGY/modeling_strategy/REDUCED_ORDER_OPERATOR_AND_RESERVOIR_PLAN.md)
- [Uncertainty/OOD Plan](02_SYSTEM_METHODOLOGY/modeling_strategy/UNCERTAINTY_OOD_AND_SUPPORT_PLAN.md)
- [Experiment Registry](02_SYSTEM_METHODOLOGY/experiment_design/EXPERIMENT_REGISTRY.md)
- [Hypothesis-to-Experiment Matrix](02_SYSTEM_METHODOLOGY/experiment_design/HYPOTHESIS_TO_EXPERIMENT_MATRIX.md)
- [Acceptance Criteria](02_SYSTEM_METHODOLOGY/experiment_design/ACCEPTANCE_CRITERIA.md)
- [Methodology Source Registry](02_SYSTEM_METHODOLOGY/sources/METHODOLOGY_SOURCE_REGISTRY.md)

## Step 3 — Technical Model Design

- [Step 3 Entry Contract](03_TECHNICAL_MODEL_DESIGN/README.md)

Step 3 will freeze exact sample rate, history/receptive field, feature contract, filter coefficients, effective model order, temporal architecture, losses, calibration method and deployment configuration only after empirical evidence allows those decisions.

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
