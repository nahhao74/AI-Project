# Gap-to-Contribution Traceability Matrix

| Gap ID | Literature evidence | Candidate mechanism | Planned evidence | Contribution only if... |
|---|---|---|---|---|
| GAP-REP-001 | Nano SO(3) processing; wind-data rotation [SRC-010,018](../sources/SOURCE_REGISTRY.md#src-010) | heading frame, relative SO(3), motor modes | raw-vs-canonical ablation | same/better error with lower capacity/latency |
| GAP-ACT-001 | motor delay; Pelican/VID command+actual [SRC-009,020,023](../sources/SOURCE_REGISTRY.md#src-009) | explicit actuator model | direct vs factorized vs oracle | factorization recovers oracle gap |
| GAP-NOM-001 | hybrid models; SINDYc/E-SINDy [SRC-003,006,025,026](../sources/SOURCE_REGISTRY.md#src-003) | VARX/SINDy nominal + residual NN | pure NN vs hybrid | total model gets better Pareto |
| GAP-MEM-001 | TCN/Mamba evidence [SRC-032,015](../sources/SOURCE_REGISTRY.md#src-032) | residual-spectrum-driven backbone | GRU/TCN/Mamba fair benchmark | selected model justified by residual memory + Pareto |
| GAP-HOR-001 | long-horizon compounding; STM32 N-step cost [SRC-007,010](../sources/SOURCE_REGISTRY.md#src-007) | direct multi-horizon decoder | recursive vs direct | error or total latency improves |
| GAP-DIST-001 | VID force, AMOVFLY wind [SRC-022,024](../sources/SOURCE_REGISTRY.md#src-022) | generic disturbance latent + supervised heads | label-specific evaluation | semantics remain dataset-valid |
| GAP-REL-001 | WM survey, DroneDiffusion, UL-MPC [SRC-001,008,017](../sources/SOURCE_REGISTRY.md#src-001) | calibrated uncertainty + support gate | calibration/OOD tests | high-error/OOD cases become detectable |
| GAP-LAT-001 | motor delay + embedded dynamics cost [SRC-009,010,012](../sources/SOURCE_REGISTRY.md#src-009) | usable prediction horizon | E2E latency + horizon | quality gain survives latency subtraction |
| GAP-EVAL-001 | whole-flight wind generalization gap [SRC-018](../sources/SOURCE_REGISTRY.md#src-018) | flight-level split policy | random vs whole-flight comparison | final claims based on whole-flight evidence |

## Governance rule

Không được viết `CLAIM-*` từ một `GAP-*` nếu chưa có:

```text
HYPOTHESIS
→ PREDEFINED EXPERIMENT
→ VALID RESULT
→ ENGINEERING DECISION
```
