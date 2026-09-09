# Source Registry

**Evidence cutoff:** 2026-09-09  
**Policy:** ưu tiên primary peer-reviewed papers và official dataset/benchmark sources. Preprints/frontier papers được đánh dấu rõ.

> Issue date sau 2026-09-09 không có nghĩa source không tồn tại: một số article đã `available online` nhưng được gán volume/issue tháng 10/2026. Các source đó chỉ được dùng theo evidence thực tế của paper, không suy rộng.

---

<a id="src-001"></a>
## SRC-001 — World Models for Physical AI Survey

S. Kirchner, N. Purschke, A. Knoll, **“A survey of world models for physical AI with uncertainty representation and control,”** *Discover Artificial Intelligence*, 2026. Published 2026-09-03.  
URL: https://link.springer.com/article/10.1007/s44163-026-02122-1  
Type: peer-reviewed open-access survey.  
Used for: definition of world models; compounding error; uncertainty calibration; planner exploitation; real-time/control coupling.

<a id="src-002"></a>
## SRC-002 — Deep Learning a Quadrotor Dynamic Model for Multi-Step Prediction

N. Mohajerin, M. Mozifian, S. L. Waslander, **“Deep Learning a Quadrotor Dynamic Model for Multi-Step Prediction,”** ICRA 2018. DOI 10.1109/ICRA.2018.8460840.  
URL: https://doi.org/10.1109/ICRA.2018.8460840  
Metadata: https://dblp.org/rec/conf/icra/MohajerinMW18.html  
Used for: early real-flight hybrid RNN multi-step dynamics precedent.

<a id="src-003"></a>
## SRC-003 — Multistep Prediction with RNNs

N. Mohajerin, S. L. Waslander, **“Multistep Prediction of Dynamic Systems With Recurrent Neural Networks,”** *IEEE TNNLS*, 2019, 30(11), 3370–3383. DOI 10.1109/TNNLS.2019.2891257.  
URL: https://arxiv.org/abs/1806.00526  
Used for: RNN initialization; real aerial-vehicle multi-step prediction; hybrid model precedent.

<a id="src-004"></a>
## SRC-004 — End2End-TCN

Multi-step quadrotor dynamics prediction using temporal convolution and future control conditioning.  
URL: https://arxiv.org/abs/2110.04182  
Used for: direct multi-step temporal convolution and future-control conditioning precedent.

<a id="src-005"></a>
## SRC-005 — PI-TCN

A. Saviolo, G. Li, G. Loianno, **“Physics-Inspired Temporal Learning of Quadrotor Dynamics for Accurate Model Predictive Trajectory Tracking,”** 2022.  
URL: https://arxiv.org/abs/2206.03305  
Used for: physics-inspired TCN; learned model + MPC; structured temporal learning precedent.

<a id="src-006"></a>
## SRC-006 — NeuroBEM

L. Bauersfeld et al., **“NeuroBEM: Hybrid Aerodynamic Quadrotor Model,”** RSS 2021.  
URL: https://arxiv.org/abs/2106.08015  
Dataset: https://rpg.ifi.uzh.ch/neuro_bem/Readme.html  
Used for: hybrid aerodynamic residual modeling; aggressive real flight.

<a id="src-007"></a>
## SRC-007 — Learning Long-Horizon Predictions for Quadrotor Dynamics

P. P. Rao, A. Saviolo, T. C. Ferrari, G. Loianno, 2024.  
URL: https://arxiv.org/abs/2407.12964  
Used for: architecture/history/multi-step loss analysis; decoupled dynamics; compounding error.

<a id="src-008"></a>
## SRC-008 — DroneDiffusion

A. Das et al., **“DroneDiffusion: Robust Quadrotor Dynamics Learning with Diffusion Models,”** 2024.  
URL: https://arxiv.org/abs/2409.11292  
Used for: multimodal uncertainty and real-flight dynamics generalization precedent.

<a id="src-009"></a>
## SRC-009 — Data-Driven System Identification Subject to Motor Delays

J. Eschmann, D. Albani, G. Loianno, **“Data-Driven System Identification of Quadrotors Subject to Motor Delays,”** 2024.  
URL: https://arxiv.org/abs/2404.07837  
Used for: motor-delay importance; proprioceptive identification of actuator dynamics.

<a id="src-010"></a>
## SRC-010 — Nonlinear System Identification for a Nano-Drone Benchmark

R. Busetto, E. Cereda, M. Forgione, G. Maroni, D. Piga, D. Palossi, **“Nonlinear system identification for a nano-drone benchmark,”** *Control Engineering Practice*, Vol. 172, 2026, 106871.  
DOI: https://doi.org/10.1016/j.conengprac.2026.106871  
Open benchmark: https://www.nonlinearbenchmark.org/benchmarks/nano-drone  
Used for: ~75k real samples; 100 Hz; 0.5 s multi-step benchmark; preprocessing; motor alignment; SO(3) filtering; STM32 inference cost.

<a id="src-011"></a>
## SRC-011 — Nano-Drone Official Repository

IDSIA Robotics, **Nano-Quadrotor System Identification Benchmark**.  
URL: https://github.com/idsia-robotics/nanodrone-sysid-benchmark  
Type: official code/data repository.  
Used for: reproducibility, reference models, benchmark protocol.

<a id="src-012"></a>
## SRC-012 — Tiny Learning-Based MPC

B. Akbari, J. Frank, M. Greeff, **“Tiny Learning-Based MPC for Multirotors: Solver-Aware Learning for Efficient Embedded Predictive Control,”** 2024.  
URL: https://arxiv.org/abs/2410.23634  
Used for: 100 Hz learning-based MPC on tiny multirotor/Teensy; solver-aware embedded design.

<a id="src-013"></a>
## SRC-013 — Real-time Neural MPC

T. Salzmann et al., **“Real-time Neural-MPC: Deep Learning Model Predictive Control for Quadrotors and Agile Robotic Platforms,”** 2022.  
URL: https://arxiv.org/abs/2203.07747  
Used for: real-time integration of neural dynamics with 50 Hz quadrotor MPC.

<a id="src-014"></a>
## SRC-014 — Mamba

A. Gu, T. Dao, **“Mamba: Linear-Time Sequence Modeling with Selective State Spaces,”** 2023.  
URL: https://arxiv.org/abs/2312.00752  
Used for: selective SSM formulation and linear sequence scaling.

<a id="src-015"></a>
## SRC-015 — Parameter-Modulation Mamba for Windy Quadrotor Dynamics

**“Parameter-modulation state space model for quadrotor control in windy environments,”** *Engineering Applications of Artificial Intelligence*, available online 2026; issue date 2026-10-15.  
URL: https://www.sciencedirect.com/science/article/abs/pii/S0952197626021238  
Evidence scope: RotorPy/high-fidelity simulation; no real hardware validation reported in source at cutoff.  
Used for: Mamba/TCN/LSTM parameter and inference comparison; wind-conditioned SSM.

<a id="src-016"></a>
## SRC-016 — PI-WAN

M. Wang et al., **“PI-WAN: A Physics-Informed Wind-Adaptive Network for Quadrotor Dynamics Prediction in Unknown Environments,”** IROS 2025.  
URL: https://arxiv.org/abs/2507.00816  
IEEE: https://ieeexplore.ieee.org/document/11247234/  
Used for: physics-informed wind-adaptive TCN; simulation + real-flight validation.

<a id="src-017"></a>
## SRC-017 — Uncertainty-Aware Learning-Based MPC

**“Uncertainty-aware model predictive control with learning-based residual dynamics compensation for agile quadrotor trajectory tracking,”** *Chinese Journal of Aeronautics*, available online 2026-08-11.  
URL: https://www.sciencedirect.com/science/article/pii/S1000936126003626  
Evidence scope: high-fidelity Gazebo simulation in published abstract.  
Used for: mean/covariance residual model and chance-constrained MPC.

<a id="src-018"></a>
## SRC-018 — Wind Estimation with Data Rotation and Reduction

**“Wind estimation by multirotor drone state using machine learning with data rotation and reduction,”** *Measurement*, 2022.  
URL: https://www.sciencedirect.com/science/article/pii/S0263224122007151  
Used for: random-sample vs complete-flight generalization gap; rotation normalization; GRU result.

<a id="src-019"></a>
## SRC-019 — Deep Learning Wind Estimation from UAV Telemetry

**“Deep learning-based wind estimation from multirotor UAV telemetry,”** *Aerospace Science and Technology*, available online 2026; issue date October 2026.  
URL: https://www.sciencedirect.com/science/article/pii/S127096382600564X  
Evidence: 1,750 real free-flight missions; ground anemometer reference.  
Used for: telemetry-only wind feasibility and ground-reference spatial mismatch.

<a id="src-020"></a>
## SRC-020 — Pelican Quadrotor Dataset

WAVELab, **Pelican Quadrotor Dataset**.  
URL: https://github.com/wavelab/pelican_dataset  
Evidence: 54 flights; Vicon; actual motor speeds; commanded motor speeds; velocities/body rates.  
Used for: command→actual actuator modeling feasibility.

<a id="src-021"></a>
## SRC-021 — NeuroBEM Dataset

UZH RPG, **NeuroBEM Dataset**.  
URL: https://rpg.ifi.uzh.ch/neuro_bem/Readme.html  
Evidence: 1 h 15 min agile real flight at 400 Hz; motor speed, derivative, battery, full dynamics.  
Used for: aggressive dynamics/aerodynamic residual feasibility.

<a id="src-022"></a>
## SRC-022 — Visual-Inertial-Dynamical Multirotor Dataset Paper

K. Zhang et al., **“The Visual-Inertial-Dynamical Multirotor Dataset,”** 2021.  
URL: https://arxiv.org/abs/2103.11152  
Used for: dynamical data, motor signals and external-force ground truth.

<a id="src-023"></a>
## SRC-023 — VID Official Dataset

ZJU FAST Lab, **VID-Dataset**.  
URL: https://github.com/ZJU-FAST-Lab/VID-Dataset  
Evidence: target RPM, measured motor RPM/current, control inputs, motion capture, force-sensor sequences.  
Used for: actuator and disturbance evaluation.

<a id="src-024"></a>
## SRC-024 — AMOVFLY Dataset

Y. Hu et al., **AMOVFLY Dataset** (repository states associated paper submitted to IEEE T-RO).  
URL: https://github.com/YujiaoHu/AMOVFLY-Dataset  
Evidence: 270+ flights, 46.1+ h, 3 UAVs, wind speed/direction, payload/altitude/speed scenarios.  
Used for: environmental/wind generalization feasibility.  
Status note: dataset is primary source; associated paper status should be rechecked before formal publication claim.

<a id="src-025"></a>
## SRC-025 — SINDYc

S. L. Brunton, J. L. Proctor, J. N. Kutz, **“Sparse Identification of Nonlinear Dynamics with Control (SINDYc),”** IFAC-PapersOnLine, 2016.  
URL: https://www.sciencedirect.com/science/article/pii/S2405896316318298  
Preprint: https://arxiv.org/abs/1605.06682  
Used for: sparse nonlinear dynamics with external inputs/control.

<a id="src-026"></a>
## SRC-026 — Ensemble-SINDy

U. Fasel, J. N. Kutz, B. W. Brunton, **“Ensemble-SINDy: Robust sparse model discovery in the low-data, high-noise limit, with active learning and control,”** *Proceedings of the Royal Society A*, 2022.  
URL: https://pmc.ncbi.nlm.nih.gov/articles/PMC9006119/  
Used for: robust sparse discovery and term-inclusion uncertainty.

<a id="src-027"></a>
## SRC-027 — Symbolic Regression for Quadrotor Dynamics

W. Fang, Z. Chen, **“A Symbolic Regression Method for Dynamic Modeling and Control of Quadrotor UAVs,”** 2021.  
URL: https://arxiv.org/abs/2105.03032  
Used for: evidence that symbolic quadrotor modeling already exists.

<a id="src-028"></a>
## SRC-028 — Cascaded INDI Disturbance Rejection

E. J. J. Smeur, G. C. H. E. de Croon, Q. Chu, **“Cascaded incremental nonlinear dynamic inversion for MAV disturbance rejection,”** *Control Engineering Practice*, 2018, 73:79–90.  
URL: https://www.sciencedirect.com/science/article/pii/S0967066118300030  
Used for: incremental representation; accelerometer-based disturbance/control-effect principle; wind-tunnel evidence.

<a id="src-029"></a>
## SRC-029 — High-Gain Disturbance Observer

M. Izadi, R. Faieghi, **“High-gain disturbance observer for robust trajectory tracking of quadrotors,”** *Control Engineering Practice*, 2024, 145:105854.  
URL: https://www.sciencedirect.com/science/article/pii/S0967066124000145  
Used for: HGDO candidate and laboratory evidence.

<a id="src-030"></a>
## SRC-030 — Unknown-Input-Observer Wind Estimation

**“Wind gust estimation for precise quasi-hovering control of quadrotor aircraft,”** *Control Engineering Practice*, 2021, 116:104930.  
URL: https://www.sciencedirect.com/science/article/pii/S0967066121002070  
Used for: UIO-based unknown wind input estimation precedent.

<a id="src-031"></a>
## SRC-031 — Data-Driven Identification Tutorial

Y. Wi et al., **“Data-driven identification of quadrotor dynamics: a tutorial,”** IFAC-PapersOnLine, 2024.  
URL: https://www.sciencedirect.com/science/article/pii/S2405896324013132  
Used for: closed-loop quadrotor identification methodology; frequency/time-domain validation context.

<a id="src-032"></a>
## SRC-032 — Generic TCN Sequence Modeling

S. Bai, J. Z. Kolter, V. Koltun, **“An Empirical Evaluation of Generic Convolutional and Recurrent Networks for Sequence Modeling,”** 2018.  
URL: https://arxiv.org/abs/1803.01271  
Used for: TCN as strong causal sequence-modeling baseline.

<a id="src-033"></a>
## SRC-033 — Feedback World Model

T. An et al., **“Feedback World Model Enables Precise Guidance of Diffusion Policy,”** preprint, 2026.  
URL: https://arxiv.org/abs/2605.15705  
Used only as future-direction evidence: inference-time residual feedback / observer-like correction.  
Not core V1 evidence for UAV dynamics.

<a id="src-034"></a>
## SRC-034 — Mapping-Aware World Model for Quadrotor Flight

X. Zhang et al., **“MAD: Mapping-Aware World Models for Agile Quadrotor Flight,”** preprint, 2026.  
URL: https://arxiv.org/abs/2606.04534  
Used for: scope differentiation between visual-navigation world models and low-level dynamics world models.
