# Dataset Comparison Matrix

| Dataset | Real flight | Command | Actual motor/RPM | Full state | IMU | Wind GT | External force GT | Multi-airframe | Best scientific role |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---|
| Pelican [SRC-020](../sources/SOURCE_REGISTRY.md#src-020) | Yes | **Yes** | **Yes** | Yes | collection context | No | No | No | command→actuator→state |
| Nano 2026 [SRC-010](../sources/SOURCE_REGISTRY.md#src-010) | Yes | benchmark motor input | **Yes** | **Yes** | Yes | No | No | No | standardized multi-step + STM32 |
| NeuroBEM [SRC-021](../sources/SOURCE_REGISTRY.md#src-021) | Yes | not core ready field | **Yes** | **Yes** | fused/derived dynamics | No | residual recomputable, not force-sensor GT | No | aggressive/aero residual |
| VID [SRC-022/023](../sources/SOURCE_REGISTRY.md#src-022) | Yes | **target RPM/current** | **Yes** | Yes | **Yes** | No | **Yes in dedicated sequences** | No | actuator + external force |
| AMOVFLY [SRC-024](../sources/SOURCE_REGISTRY.md#src-024) | Yes | limited for low-level actuation | No detailed chain in ready data | flight state | orientation/accel sources | **Yes** | No | **3 UAVs** | wind/context/cross-UAV |

## Key conclusion

Không có một public dataset trong shortlist vừa có:

\[
\text{future command}
+
\text{actual actuator}
+
\text{full state}
+
\text{wind GT}
+
\text{force GT}
+
\text{multiple airframes}
\]

đồng thời.

Do đó evidence phải modular. Việc merge tất cả dataset trước khi map semantics sẽ làm giảm scientific validity chứ không tăng nó.
