# Hypothesis-to-Experiment Matrix

| Step 1 hypothesis | Primary experiment(s) | Primary dataset candidate | Decision evidence |
|---|---|---|---|
| HYP-REP-01 | EXP-100, EXP-130 | Nano/Pelican/NanoBench | error vs model size/latency; yaw equivariance |
| HYP-ACT-01 | EXP-200, EXP-210 | Pelican/VID | actuator trajectory + state error |
| HYP-ACT-02 | EXP-210 | Pelican/VID | oracle-gap recovery |
| HYP-ACT-03 | EXP-110, EXP-200 | Pelican/VID | T10/T50/T90 or response-profile error |
| HYP-NOM-01 | EXP-300, EXP-310 | Nano/NeuroBEM/Pelican | variance explained; residual energy |
| HYP-NOM-02 | EXP-500 | Nano/Pelican | pure NN vs explicit+small residual NN Pareto |
| HYP-MEM-01 | EXP-320 | residuals from explicit models | ACF/PSD/coherence/whiteness |
| HYP-MEM-02 | EXP-400 | same residual contract | TCN/GRU vs alternatives under compute report |
| HYP-MEM-03 | EXP-400, EXP-800 | same residual contract | Mamba gain vs measured latency/memory overhead |
| HYP-HOR-01 | EXP-410 | Nano/Pelican | direct vs recursive horizon error and total latency |
| HYP-DIST-01 | EXP-600 | NeuroBEM/VID/AMOVFLY/NanoBench context | unseen-regime error |
| HYP-DIST-02 | EXP-600 | AMOVFLY | wind-conditioned metrics only where wind is measured |
| HYP-DIST-03 | EXP-600 | VID/qualified state-space datasets | observer feature information gain vs assumptions/noise |
| HYP-REL-01 | EXP-700 | all held-out datasets | NLL + empirical coverage/calibration |
| HYP-REL-02 | EXP-710 | intentionally shifted regimes | OOD AUROC / confident-error detection |
| HYP-REL-03 | EXP-710 | same | error–coverage / abstention curve |
| HYP-LAT-01 | EXP-800, EXP-900 | host then target hardware | accuracy–latency Pareto |
| HYP-LAT-02 | EXP-100/300/310/500/800 | modular | model/MAC reduction with preserved useful accuracy |
| HYP-LAT-03 | EXP-800 | all final candidates | preprocessing+model+postprocess p95/p99 vs model.forward |
| HYP-EVAL-01 | EXP-010 plus split sensitivity | Pelican/NanoBench/AMOVFLY | random-window vs whole-flight evaluation gap |

## Rule

The matrix is a planning authority, not evidence of success. A hypothesis remains `PROPOSED_NOT_YET_TESTED` until a valid experiment result exists in Step 4.
