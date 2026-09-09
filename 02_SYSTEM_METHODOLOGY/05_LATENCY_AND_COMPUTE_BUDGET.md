# 05 — Latency and Compute Budget

## 1. Latency is a methodology variable

A World Model used for control is useful only if its prediction remains ahead of the physical system when the result becomes available.

```math
\tau_{pipeline}=\tau_{buffer}+\tau_{pre}+\tau_{explicit}+\tau_{NN}+\tau_{decode}+\tau_{reliability}+\tau_{post}.
```

Broader action-to-effect timing:

```math
\tau_{effective}=\tau_{source-age}+\tau_{pipeline}+\tau_{command/transport}+\tau_{actuator/effect}.
```

## 2. Usable-horizon working metric

```math
H_{usable}=H_{accurate}-\tau_{effective}.
```

This is a **project-defined quantity**. Step 3/4 must freeze the accuracy threshold, measurement boundary and effect-onset semantics.

## 3. Measurement protocol

For every deployable candidate:

- batch size = 1;
- fixed input shape;
- warm-up before timed runs;
- enough repetitions to estimate tails;
- synchronize asynchronous accelerator execution;
- record CPU/GPU/MCU and software versions;
- control/pin threads where practical;
- report mean, median, p95, p99 and max;
- report peak memory, model bytes and MAC/FLOP estimate.

PyTorch timing must account for asynchronous CUDA execution [MSRC-029](sources/METHODOLOGY_SOURCE_REGISTRY.md#msrc-029).

## 4. Parameter count is not latency

Nano-Drone profiling demonstrates that operator structure matters; zero-trainable-parameter physics may still cost nontrivial compute. A smaller Mamba also does not automatically outrun a TCN at short sequence length. Therefore selection is based on measured Pareto performance:

```math
\mathcal P=\{E,T_{p99},RAM,model\ bytes,MACs\}.
```

## 5. Offline/runtime separation

Welch analysis, symbolic search, covariance fitting and hyperparameter search are offline costs. Any operation executed per prediction counts toward runtime.

## 6. Hardware claim policy

```text
Laptop/desktop timing → HOST_INFERENCE_LATENCY
Jetson/MCU timing      → TARGET_HARDWARE_LATENCY
Onboard timing         → only when physically measured onboard
```

No onboard claim is inferred from desktop timing.

## 7. Update-rate feasibility

No final rate is frozen. At 100 Hz:

```math
T_s=10\;\text{ms}.
```

The minimum real-time condition on a claimed target is $T_{pipeline,p99}<10$ ms. A 50% budget is a project headroom candidate, not a literature standard.
