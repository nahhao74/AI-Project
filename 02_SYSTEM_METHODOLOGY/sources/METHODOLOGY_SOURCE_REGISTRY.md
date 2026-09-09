# Step 2 Methodology Source Registry

**Evidence cutoff:** 2026-09-09  
**Policy:** Step 2 sources justify execution methodology and candidate ranges. They do not determine final project parameters by themselves.

---

<a id="msrc-001"></a>
## MSRC-001 — Nano-Drone System Identification Benchmark Paper

R. Busetto et al., **“Nonlinear system identification for a nano-drone benchmark,”** *Control Engineering Practice*, 2026, 172:106871.  
DOI: https://doi.org/10.1016/j.conengprac.2026.106871  
Used for: real-flight benchmark design, 100 Hz processing, multi-step evaluation, temporal alignment, $SO(3)$ handling and embedded profiling.

<a id="msrc-002"></a>
## MSRC-002 — Nano-Drone System Identification Official Repository

IDSIA Robotics, **Nano-Quadrotor System Identification Benchmark**.  
https://github.com/idsia-robotics/nanodrone-sysid-benchmark  
Used for: reproducible reference models, published trajectory split, preprocessing and profiling workflow.

<a id="msrc-003"></a>
## MSRC-003 — Long-Horizon Quadrotor Dynamics

P. P. Rao et al., **“Learning Long-Horizon Predictions for Quadrotor Dynamics,”** 2024.  
https://arxiv.org/abs/2407.12964  
Used for: history length, multi-step loss, decoupling and compounding-error methodology.

<a id="msrc-004"></a>
## MSRC-004 — NanoBench 2026

S. I. Ullah, J. Baca, **“NanoBench: A Multi-Task Benchmark Dataset for Nano-Quadrotor System Identification, Control, and State Estimation,”** 2026.  
https://github.com/syediu/nanobench-iros2026  
Evidence: 170 flight recordings, 27 trajectory types, ~603,942 100 Hz timesteps and 51 synchronized columns including Vicon, IMU, EKF, per-motor PWM, controller internals and battery.  
Important semantic note: motor columns are PWM commands, not actual rotor-speed measurements.

<a id="msrc-005"></a>
## MSRC-005 — Pelican Quadrotor Dataset

WAVELab, **Pelican Quadrotor Dataset**.  
https://github.com/wavelab/pelican_dataset  
Evidence: 54 flights; `Motors_CMD`, actual `Motors`, Vicon pose, derived velocity/body rate.  
Used for: command→actual actuator modeling. Derived `Vel` and `pqr` require quality audit.

<a id="msrc-006"></a>
## MSRC-006 — NeuroBEM

L. Bauersfeld et al., **“NeuroBEM: Hybrid Aerodynamic Quadrotor Model,”** RSS 2021.  
https://arxiv.org/abs/2106.08015  
Dataset: https://rpg.ifi.uzh.ch/neuro_bem/Readme.html  
Used for: aggressive 400 Hz real-flight dynamics and aerodynamic residual analysis.

<a id="msrc-007"></a>
## MSRC-007 — Visual-Inertial-Dynamical Dataset

K. Zhang et al., **“The Visual-Inertial-Dynamical Multirotor Dataset,”** 2021.  
https://arxiv.org/abs/2103.11152  
Repository: https://github.com/ZJU-FAST-Lab/VID-Dataset  
Used for: hardware-synchronized target RPM/current, measured RPM/current, state ground truth and force-sensor sequences.

<a id="msrc-008"></a>
## MSRC-008 — Motor Delay Identification

J. Eschmann, D. Albani, G. Loianno, **“Data-Driven System Identification of Quadrotors Subject to Motor Delays,”** 2024.  
https://arxiv.org/abs/2404.07837  
Used for: preserving physical command→actuator delay as a scientific quantity.

<a id="msrc-009"></a>
## MSRC-009 — AMOVFLY Dataset

Y. Hu et al., **AMOVFLY Dataset**.  
https://github.com/YujiaoHu/AMOVFLY-Dataset  
Evidence: 270+ flights, 46.1+ hours, 3 UAVs, measured wind, payload/battery/context.  
Used for: environment/OOD methodology. Column semantics are audited independently rather than trusted by name alone.

<a id="msrc-010"></a>
## MSRC-010 — Continuous Rotation Representations

Y. Zhou et al., **“On the Continuity of Rotation Representations in Neural Networks,”** CVPR 2019.  
https://arxiv.org/abs/1812.07035  
Used for: 6D continuous rotation representation challenger and quaternion-representation limitations.

<a id="msrc-011"></a>
## MSRC-011 — Lie-Group / SO(3) Geometry Reference

J. Solà, J. Deray, D. Atchuthan, **“A micro Lie theory for state estimation in robotics,”** 2018.  
https://arxiv.org/abs/1812.01537  
Used for: rotation composition, exponential/logarithmic map and frame-consistent $SO(3)$ reasoning.

<a id="msrc-012"></a>
## MSRC-012 — SciPy Zero-Phase Filtering Reference

SciPy documentation, `scipy.signal.filtfilt` / `sosfiltfilt`.  
https://docs.scipy.org/doc/scipy/reference/generated/scipy.signal.filtfilt.html  
Used for: explicit confirmation that forward-backward filtering is zero-phase/non-causal for runtime semantics.

<a id="msrc-013"></a>
## MSRC-013 — Welch / CSD / Coherence Definitions

SciPy signal-processing documentation.  
https://docs.scipy.org/doc/scipy/reference/generated/scipy.signal.welch.html  
https://docs.scipy.org/doc/scipy/reference/generated/scipy.signal.csd.html  
https://docs.scipy.org/doc/scipy/reference/generated/scipy.signal.coherence.html  
Used for: PSD, cross-spectrum and magnitude-squared coherence diagnostics.

<a id="msrc-014"></a>
## MSRC-014 — Wind Estimation with Data Rotation and Reduction

**“Wind estimation by multirotor drone state using machine learning with data rotation and reduction,”** *Measurement*, 2022.  
https://www.sciencedirect.com/science/article/pii/S0263224122007151  
Used for: coordinate rotation methodology and complete-flight generalization evidence.

<a id="msrc-015"></a>
## MSRC-015 — Whitening / PCA Reference

A. Kessy, A. Lewin, K. Strimmer, **“Optimal Whitening and Decorrelation,”** *The American Statistician*, 2018.  
https://arxiv.org/abs/1512.00809  
Used for: whitening definitions and distinction between whitening and dimensionality reduction.

<a id="msrc-016"></a>
## MSRC-016 — Ledoit–Wolf Shrinkage Covariance

O. Ledoit, M. Wolf, **“A well-conditioned estimator for large-dimensional covariance matrices,”** *Journal of Multivariate Analysis*, 2004.  
https://doi.org/10.1016/S0047-259X(03)00096-4  
Used for: regularized covariance in whitening/Mahalanobis support.

<a id="msrc-017"></a>
## MSRC-017 — Mahalanobis OOD Baseline

K. Lee et al., **“A Simple Unified Framework for Detecting Out-of-Distribution Samples and Adversarial Attacks,”** NeurIPS 2018.  
https://arxiv.org/abs/1807.03888  
Used for: Mahalanobis-distance support/OOD methodology; not treated as universally sufficient.

<a id="msrc-018"></a>
## MSRC-018 — SINDYc

S. L. Brunton, J. L. Proctor, J. N. Kutz, **“Sparse Identification of Nonlinear Dynamics with Control (SINDYc),”** 2016.  
https://arxiv.org/abs/1605.06682  
Used for: sparse controlled-system nominal-model candidate.

<a id="msrc-019"></a>
## MSRC-019 — Ensemble-SINDy

U. Fasel, J. N. Kutz, B. W. Brunton, **“Ensemble-SINDy,”** *Proceedings of the Royal Society A*, 2022.  
https://pmc.ncbi.nlm.nih.gov/articles/PMC9006119/  
Used for: robust sparse discovery in low-data/high-noise settings.

<a id="msrc-020"></a>
## MSRC-020 — Cascaded INDI

E. J. J. Smeur, G. C. H. E. de Croon, Q. Chu, **“Cascaded incremental nonlinear dynamic inversion for MAV disturbance rejection,”** *Control Engineering Practice*, 2018.  
https://www.sciencedirect.com/science/article/pii/S0967066118300030  
Used for: incremental action-effect representation candidate.

<a id="msrc-021"></a>
## MSRC-021 — High-Gain Disturbance Observer

M. Izadi, R. Faieghi, **“High-gain disturbance observer for robust trajectory tracking of quadrotors,”** *Control Engineering Practice*, 2024.  
https://www.sciencedirect.com/science/article/pii/S0967066124000145  
Used for: HGDO candidate and noise/bandwidth trade-off.

<a id="msrc-022"></a>
## MSRC-022 — Unknown-Input Observer Wind Estimation

**“Wind gust estimation for precise quasi-hovering control of quadrotor aircraft,”** *Control Engineering Practice*, 2021.  
https://www.sciencedirect.com/science/article/pii/S0967066121002070  
Used for: UIO candidate under explicit model/rank assumptions.

<a id="msrc-023"></a>
## MSRC-023 — End2End-TCN

https://arxiv.org/abs/2110.04182  
Used for: direct multi-step causal temporal convolution with future-control conditioning.

<a id="msrc-024"></a>
## MSRC-024 — Generic TCN

S. Bai, J. Z. Kolter, V. Koltun, **“An Empirical Evaluation of Generic Convolutional and Recurrent Networks for Sequence Modeling,”** 2018.  
https://arxiv.org/abs/1803.01271  
Used for: causal/dilated TCN baseline.

<a id="msrc-025"></a>
## MSRC-025 — Mamba

A. Gu, T. Dao, **“Mamba: Linear-Time Sequence Modeling with Selective State Spaces,”** 2023.  
https://arxiv.org/abs/2312.00752  
Used for: selective-state-space challenger. Theoretical sequence complexity is not substituted for measured runtime.

<a id="msrc-026"></a>
## MSRC-026 — Group-Aware Data Splitting

Scikit-learn documentation, `GroupShuffleSplit`.  
https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GroupShuffleSplit.html  
Used for: formal implementation reference for non-overlapping group identities; project scientific unit remains whole flight/trajectory.

<a id="msrc-027"></a>
## MSRC-027 — Tiny Learning-Based MPC

B. Akbari, J. Frank, M. Greeff, **“Tiny Learning-Based MPC for Multirotors,”** 2024.  
https://arxiv.org/abs/2410.23634  
Used for: whole-graph embedded feasibility and solver/model-aware design.

<a id="msrc-028"></a>
## MSRC-028 — Real-time Neural MPC

T. Salzmann et al., **“Real-time Neural-MPC,”** 2022.  
https://arxiv.org/abs/2203.07747  
Used for: real-time learned-dynamics integration precedent.

<a id="msrc-029"></a>
## MSRC-029 — PyTorch Benchmarking / CUDA Synchronization

PyTorch documentation, benchmark/timing guidance.  
https://pytorch.org/docs/stable/benchmark_utils.html  
Used for: batch-1 latency methodology and avoiding asynchronous CUDA timing errors.

---

## Evidence rule

Literature determines **what is scientifically plausible to test**, not which mechanism wins. Final sample rates, windows, cutoffs, model sizes, VARX order, symbolic library and OOD thresholds are selected from project data under the frozen train/validation/test policy.
