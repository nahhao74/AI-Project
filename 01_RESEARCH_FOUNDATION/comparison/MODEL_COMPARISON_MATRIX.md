# Model Comparison Matrix

| Work / family | Main input | Main output | Real flight? | Temporal memory | Physics/structure | Uncertainty | Actuator timing explicit? | Key strength | Limitation relative to this project |
|---|---|---|---:|---|---|---|---|---|---|
| Mohajerin hybrid RNN 2018/19 [SRC-002/003](../sources/SOURCE_REGISTRY.md#src-002) | state + motor sequence | multi-step motion | Yes | RNN | simplified motion model + RNN | No primary | No | early strong multi-step hybrid evidence | no modern reliability/latency focus |
| End2End-TCN [SRC-004](../sources/SOURCE_REGISTRY.md#src-004) | history + future controls | multi-step state | Real-data study | TCN | learned | No primary | implicit | one-pass future-control prediction | actuation chain implicit |
| PI-TCN [SRC-005](../sources/SOURCE_REGISTRY.md#src-005) | history/control | dynamics | Yes | TCN | physics-inspired loss | No primary | implicit | structured temporal learning + MPC | physics constraints not actuation-focused |
| NeuroBEM [SRC-006](../sources/SOURCE_REGISTRY.md#src-006) | physical state/motor/context | aerodynamic force/torque/dynamics | Yes | learned residual | strong first-principles BEM | No primary | partial via motor modeling | high-speed real-flight accuracy | platform physics/parameters heavier |
| Rao 2024 [SRC-007](../sources/SOURCE_REGISTRY.md#src-007) | state/action history | long-horizon dynamics | Yes | RNN/TCN etc. | decoupled formulation | No primary | implicit | systematic long-horizon ablation | not actuation-interface study |
| DroneDiffusion [SRC-008](../sources/SOURCE_REGISTRY.md#src-008) | conditional state/action sequence | distribution over dynamics sequence | Yes | diffusion sequence | learned | Strong | implicit | multimodal uncertainty | compute cost may be high |
| Nano benchmark baselines [SRC-010](../sources/SOURCE_REGISTRY.md#src-010) | motor speed + state | 0.5 s state rollout | Yes | MLP/LSTM/hybrid | physics + residual baselines | No primary | aligned but future motor sequence supplied | reproducible + STM32 compute | future motor input can be oracle-like for planner use |
| PI-WAN [SRC-016](../sources/SOURCE_REGISTRY.md#src-016) | flight history + environment | dynamics | Yes | TCN | physics-informed | limited | implicit | wind/payload robustness | wind-aware TCN already established |
| PM-Mamba wind [SRC-015](../sources/SOURCE_REGISTRY.md#src-015) | residual history + wind | environmental residual dynamics | No hardware at cutoff | Mamba/SSM | nominal + learned residual | No primary | implicit | conditional long-memory SSM | RotorPy simulation; TCN faster in reported seq=32 benchmark |
| UL-MPC [SRC-017](../sources/SOURCE_REGISTRY.md#src-017) | nominal + state | residual mean/cov | simulation evidence | NN | residual | Strong Gaussian | implicit | covariance-aware control | no real-flight evidence in source at cutoff |
| Tiny LB-MPC [SRC-012](../sources/SOURCE_REGISTRY.md#src-012) | compact learned dynamics | MPC prediction | Yes | compact | solver-aware structural | control robustness | controller-level | 100 Hz embedded | not a general world-model architecture |
| Proposed candidate | command + causal history | actuator + multi-horizon state + uncertainty/support | Target: Yes | **to be selected** | explicit/sparse nominal + residual | Required | **Yes, primary** | actuation semantics + efficiency + reliability | unproven until Step 4 |

## Interpretation

Bảng này cho thấy không có cơ sở để claim novelty từ multi-step learning, TCN, Mamba, hybrid physics, wind adaptation hoặc uncertainty riêng lẻ.

Scientific opportunity nằm ở **interaction của các failure modes**, đặc biệt actuation availability, representation efficiency và deployment-oriented evaluation.
