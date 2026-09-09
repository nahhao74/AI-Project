# Paper Review Notes

## Review schema

Mỗi source được đọc theo:

```text
Problem
Data / validation domain
Inputs
Outputs
Model
Assumptions
Strengths
Limitations
Reusable mechanisms
Claims NOT transferable
```

---

## A. Mohajerin / Waslander — Multi-step RNN

Sources: [SRC-002](SOURCE_REGISTRY.md#src-002), [SRC-003](SOURCE_REGISTRY.md#src-003)

**Problem:** learned multi-step dynamics for aerial vehicles.  
**Data:** experimental helicopter + Pelican quadrotor.  
**Core:** RNN with learned initialization; hybrid simplified physics + RNN.  
**Takeaway:** long-horizon training and hybrid structure are established.  
**Cannot claim:** hybrid RNN itself is novel.  
**Reusable:** strong historical baseline; Pelican signal semantics.

---

## B. End2End-TCN / PI-TCN

Sources: [SRC-004](SOURCE_REGISTRY.md#src-004), [SRC-005](SOURCE_REGISTRY.md#src-005)

**Problem:** efficient temporal multi-step dynamics; physics-informed generalization.  
**Core:** causal/sparse temporal convolution, future controls, multi-step prediction.  
**Takeaway:** TCN is a domain-relevant baseline, not merely generic DL.  
**Reusable:** direct multi-horizon decoding; causal convolutions; physics constraints.  
**Gap:** command-to-actuator interface and reliability are not the central target.

---

## C. NeuroBEM

Source: [SRC-006](SOURCE_REGISTRY.md#src-006)

**Problem:** high-speed aerodynamic model defect.  
**Core:** first-principles BEM + learned residual.  
**Validation:** aggressive real flight.  
**Strength:** evidence that structured residual modeling can beat pure components.  
**Limit:** platform physics is richer/more parameterized than desired public-data V1.  
**Reusable:** learn residual rather than full dynamics; aggressive dataset.

---

## D. Rao et al. 2024

Source: [SRC-007](SOURCE_REGISTRY.md#src-007)

**Problem:** compounding error over long horizons.  
**Studied:** model architectures, historical inputs, multi-step loss, decoupling.  
**Takeaway:** history and training objective must be ablated; decoupling can simplify dynamics.  
**Project consequence:** proposed improvement must be benchmarked beyond one-step RMSE.

---

## E. DroneDiffusion

Source: [SRC-008](SOURCE_REGISTRY.md#src-008)

**Problem:** deterministic/Gaussian assumptions miss multimodal real dynamics.  
**Core:** conditional diffusion dynamics.  
**Strength:** uncertainty richness and real-flight scenarios.  
**Trade-off:** iterative generative inference may be expensive for high-rate low-level loops.  
**Project consequence:** use as reliability/uncertainty comparison concept, not default V1.

---

## F. Motor Delay Identification

Source: [SRC-009](SOURCE_REGISTRY.md#src-009)

**Problem:** actuator delay neglected in end-to-end quadrotor models.  
**Core:** data-driven/MAP estimation of motor dynamics from flight data.  
**Takeaway:** action interface requires timing semantics.  
**Project consequence:** explicit actuator model is scientifically justified.

---

## G. Nano-Drone Benchmark

Sources: [SRC-010](SOURCE_REGISTRY.md#src-010), [SRC-011](SOURCE_REGISTRY.md#src-011)

**Problem:** reproducible real nano-UAV nonlinear identification.  
**Data:** ~75k real samples, 4 motor inputs, 13-D outputs, aggressive trajectories.  
**Evaluation:** open-loop 50-step / 0.5 s, 100 Hz.  
**Preprocessing:** alignment/resampling/filtering/SO(3) handling.  
**Embedded:** reference inference on STM32.  
**Most important lesson:** preprocessing and operator-level compute are part of model quality.

---

## H. Tiny LB-MPC / Neural MPC

Sources: [SRC-012](SOURCE_REGISTRY.md#src-012), [SRC-013](SOURCE_REGISTRY.md#src-013)

**Problem:** learned dynamics can make optimization too expensive.  
**Takeaway:** high-rate learned control requires solver-aware/structured integration.  
**Project consequence:** `model.forward latency` alone is insufficient; evaluate entire prediction path.

---

## I. Mamba family

Sources: [SRC-014](SOURCE_REGISTRY.md#src-014), [SRC-015](SOURCE_REGISTRY.md#src-015)

**Mamba foundation:** selective state spaces, linear sequence scaling.  
**Quadrotor wind study:** compact two-layer Mamba/parameter modulation under RotorPy wind simulation.  
**Important result:** fewer parameters did not imply lower inference than TCN for sequence length 32 in that experiment.  
**Project decision:** Mamba remains challenger for longer residual memory, not default.

---

## J. PI-WAN

Source: [SRC-016](SOURCE_REGISTRY.md#src-016)

**Problem:** unknown environment, wind, payload, perturbation.  
**Core:** TCN + physics-informed loss + MPC.  
**Validation:** includes real flight per paper.  
**Project consequence:** wind-aware TCN and physics-informed learning are established.

---

## K. UL-MPC

Source: [SRC-017](SOURCE_REGISTRY.md#src-017)

**Problem:** model error/external disturbance uncertainty.  
**Core:** probabilistic residual mean/covariance + chance constraints.  
**Evidence at cutoff:** Gazebo/high-fidelity simulation.  
**Project consequence:** covariance output is meaningful only if calibrated and evaluation scope stated.

---

## L. Wind estimation papers

Sources: [SRC-018](SOURCE_REGISTRY.md#src-018), [SRC-019](SOURCE_REGISTRY.md#src-019)

**Strong evidence 1:** random-sample test can be substantially easier than complete unseen flights.  
**Strong evidence 2:** coordinate/data-distribution treatment matters.  
**Strong evidence 3:** wind ground truth from fixed anemometer has spatial mismatch.  
**Project consequence:** whole-flight split and label uncertainty are mandatory considerations.

---

## M. Symbolic / sparse discovery

Sources: [SRC-025](SOURCE_REGISTRY.md#src-025), [SRC-026](SOURCE_REGISTRY.md#src-026), [SRC-027](SOURCE_REGISTRY.md#src-027)

**SINDYc:** sparse nonlinear system identification with control.  
**E-SINDy:** robustness/inclusion probability via bagging.  
**Quadrotor symbolic regression:** establishes prior art.  
**Project consequence:** symbolic module must prove complexity reduction, not just interpretability.

---

## N. INDI / disturbance observers

Sources: [SRC-028](SOURCE_REGISTRY.md#src-028), [SRC-029](SOURCE_REGISTRY.md#src-029), [SRC-030](SOURCE_REGISTRY.md#src-030)

**INDI:** incremental response measured through acceleration, reduced model dependency, real wind-tunnel evidence.  
**HGDO:** real-time disturbance observer with lab validation.  
**UIO:** unknown wind input estimation under quasi-hover assumptions.  
**Project consequence:** these are control/observer-inspired candidate features; they are not neural backbone replacements by default.

---

## O. Physical-AI World Model Survey

Source: [SRC-001](SOURCE_REGISTRY.md#src-001)

Critical deployment themes: partial observability; long-horizon compounding error; uncertainty calibration; planner exploitation; distribution shift; physical constraints; real-time control coupling.

---

## P. MAD visual world model

Source: [SRC-034](SOURCE_REGISTRY.md#src-034)

**Purpose:** visual navigation/occupancy/visibility world model.  
**Why included:** prevent misuse of broad “UAV world model novelty” claim.  
**Project scope remains:** low-level action→physical dynamics.
