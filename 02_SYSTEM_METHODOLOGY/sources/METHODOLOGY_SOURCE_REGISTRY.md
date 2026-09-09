# Step 2 Methodology Source Registry

**Evidence cutoff:** 2026-09-09  
**Purpose:** sources used to justify execution methodology, preprocessing, model comparison and validation rules. Step 1 remains the authority for broad research motivation.

---

## METH-SRC-001 — Nano-Drone System Identification Benchmark

R. Busetto et al., **“Nonlinear system identification for a nano-drone benchmark,”** *Control Engineering Practice*, 2026, 172:106871.  
DOI: https://doi.org/10.1016/j.conengprac.2026.106871  
Repository: https://github.com/idsia-robotics/nanodrone-sysid-benchmark  
Used for: real-flight benchmark design; trajectory-level split; 100 Hz processing; motor/state alignment; $SO(3)$ handling; embedded profiling.

## METH-SRC-002 — NanoBench 2026

S. I. Ullah, J. Baca, **“NanoBench: A Multi-Task Benchmark Dataset for Nano-Quadrotor System Identification, Control, and State Estimation,”** preprint, 2026.  
Repository: https://github.com/syediu/nanobench-iros2026  
Used for: 170 flight recordings, 27 trajectory types, ~603k 100 Hz timesteps, 51 synchronized Vicon/IMU/EKF/PWM/controller/battery signals; command-side and regime methodology.  
Important semantic limitation: motor columns are PWM commands, not actual rotor-speed measurements.

## METH-SRC-003 — Pelican Dataset

WAVELab, **Pelican Quadrotor Dataset**.  
Repository: https://github.com/wavelab/pelican_dataset  
Used for: 54 flights with `Motors_CMD` and `Motors`; command→actual actuator methodology.  
Note: published `Vel` and `pqr` are numerically derived and must be quality-audited.

## METH-SRC-004 — VID Dataset

K. Zhang et al., **“The Visual-Inertial-Dynamical Multirotor Dataset,”** 2021.  
Paper: https://arxiv.org/abs/2103.11152  
Repository: https://github.com/ZJU-FAST-Lab/VID-Dataset  
Used for: hardware-synchronized target RPM/current, measured RPM/current, motion ground truth, loaded/loadless regimes and force-sensor sequences.

## METH-SRC-005 — NeuroBEM

L. Bauersfeld et al., **“NeuroBEM: Hybrid Aerodynamic Quadrotor Model,”** RSS 2021.  
Paper: https://arxiv.org/abs/2106.08015  
Dataset: https://rpg.ifi.uzh.ch/neuro_bem/Readme.html  
Used for: aggressive 400 Hz real-flight dynamics and aerodynamic residual methodology.

## METH-SRC-006 — AMOVFLY

Y. Hu et al., **AMOVFLY Dataset**.  
Repository: https://github.com/YujiaoHu/AMOVFLY-Dataset  
Used for: 270+ flights, 46.1+ hours, 3 UAVs, wind/payload/battery/context and OOD methodology.  
Note: signal semantics must be independently audited; ready-data acceleration description is not treated as automatically authoritative.

## METH-SRC-007 — Motor Delay Identification

J. Eschmann, D. Albani, G. Loianno, **“Data-Driven System Identification of Quadrotors Subject to Motor Delays,”** 2024.  
https://arxiv.org/abs/2404.07837  
Used for: preserving command→actuator physical delay rather than preprocessing it away.

## METH-SRC-008 — Long-Horizon Quadrotor Dynamics

P. P. Rao et al., **“Learning Long-Horizon Predictions for Quadrotor Dynamics,”** 2024.  
https://arxiv.org/abs/2407.12964  
Used for: history/multi-step loss/decoupled prediction and compounding-error methodology.

## METH-SRC-009 — End2End-TCN

https://arxiv.org/abs/2110.04182  
Used for: direct multi-step temporal convolution with future control sequence.

## METH-SRC-010 — TCN

S. Bai, J. Z. Kolter, V. Koltun, **“An Empirical Evaluation of Generic Convolutional and Recurrent Networks for Sequence Modeling,”** 2018.  
https://arxiv.org/abs/1803.01271  
Used for: causal/dilated TCN baseline methodology.

## METH-SRC-011 — Mamba

A. Gu, T. Dao, **“Mamba: Linear-Time Sequence Modeling with Selective State Spaces,”** 2023.  
https://arxiv.org/abs/2312.00752  
Used for: selective-SSM challenger methodology; theoretical sequence scaling is not treated as measured embedded latency.

## METH-SRC-012 — Wind-Conditioned Mamba Quadrotor Study

**“Parameter-modulation state space model for quadrotor control in windy environments,”** available online 2026.  
https://www.sciencedirect.com/science/article/abs/pii/S0952197626021238  
Used for: evidence that lower parameter count does not necessarily imply lower inference latency for short sequences.

## METH-SRC-013 — SINDYc

S. L. Brunton, J. L. Proctor, J. N. Kutz, **“Sparse Identification of Nonlinear Dynamics with Control (SINDYc),”** 2016.  
https://arxiv.org/abs/1605.06682  
Used for: sparse controlled-system nominal-model candidate.

## METH-SRC-014 — Ensemble-SINDy

U. Fasel, J. N. Kutz, B. W. Brunton, **“Ensemble-SINDy,”** *Proceedings of the Royal Society A*, 2022.  
https://pmc.ncbi.nlm.nih.gov/articles/PMC9006119/  
Used for: robust sparse discovery under low-data/high-noise conditions.

## METH-SRC-015 — Cascaded INDI

E. J. J. Smeur, G. C. H. E. de Croon, Q. Chu, **“Cascaded incremental nonlinear dynamic inversion for MAV disturbance rejection,”** *Control Engineering Practice*, 2018.  
https://www.sciencedirect.com/science/article/pii/S0967066118300030  
Used for: incremental action-effect representation candidate.

## METH-SRC-016 — High-Gain Disturbance Observer

M. Izadi, R. Faieghi, **“High-gain disturbance observer for robust trajectory tracking of quadrotors,”** *Control Engineering Practice*, 2024.  
https://www.sciencedirect.com/science/article/pii/S0967066124000145  
Used for: disturbance-observer feature candidate and noise/bandwidth trade-off.

## METH-SRC-017 — Unknown-Input Observer Wind Estimation

**“Wind gust estimation for precise quasi-hovering control of quadrotor aircraft,”** *Control Engineering Practice*, 2021.  
https://www.sciencedirect.com/science/article/pii/S0967066121002070  
Used for: UIO candidate; applicability is conditional on model/rank assumptions.

## METH-SRC-018 — Tiny Learning-Based MPC

B. Akbari, J. Frank, M. Greeff, **“Tiny Learning-Based MPC for Multirotors,”** 2024.  
https://arxiv.org/abs/2410.23634  
Used for: whole-pipeline real-time/embedded design rather than parameter-count-only reasoning.

## METH-SRC-019 — Real-time Neural MPC

T. Salzmann et al., **“Real-time Neural-MPC,”** 2022.  
https://arxiv.org/abs/2203.07747  
Used for: real-time learned-dynamics integration evidence.

## METH-SRC-020 — Wind Estimation Data Rotation / Reduction

**“Wind estimation by multirotor drone state using machine learning with data rotation and reduction,”** *Measurement*, 2022.  
https://www.sciencedirect.com/science/article/pii/S0263224122007151  
Used for: whole-flight generalization gap and coordinate/data-distribution methodology.

---

## Evidence rule

No source determines a project parameter by itself. Literature establishes a plausible candidate/range; project data and validation experiments determine whether the mechanism is retained.
