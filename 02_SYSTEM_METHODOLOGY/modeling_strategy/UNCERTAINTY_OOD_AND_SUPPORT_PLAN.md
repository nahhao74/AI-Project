# Uncertainty, OOD and Support Plan

## 1. Objective

A dynamics model used for downstream decision making must estimate not only a mean prediction but also whether the prediction is supported by training evidence.

## 2. Lightweight uncertainty baseline

Primary candidate is heteroscedastic Gaussian output per horizon:

```math
p(y_h\mid z_t)=\mathcal{N}(\mu_h,\Sigma_h).
```

Diagonal covariance is the lightweight baseline. Richer covariance is considered only if calibration/generalization improves enough to justify cost.

## 3. Calibration requirements

Variance is not accepted as uncertainty evidence by itself. Required diagnostics include:

- negative log-likelihood;
- empirical interval coverage;
- calibration curve;
- interval width versus error;
- calibration by horizon and flight regime.

Example requirement:

```math
P(Y\in CI_{1-\alpha})\approx1-\alpha
```

within stated test scope.

## 4. Support / OOD candidates

### Mahalanobis

Use compact latent/context features with shrinkage covariance:

```math
d_M^2(z)=(z-\mu)^\top\hat\Sigma^{-1}(z-\mu).
```

### Local density / kNN

Challenger for non-Gaussian support geometry.

### Ensemble disagreement

Offline/reference challenger. It is not assumed deployable because multiple forward passes increase compute.

## 5. Regime-conditioned support

A single global Gaussian may be inappropriate for hover, aggressive rotation, transient actuation and wind regimes. If clustering/regime labels are stable and pre-treatment/runtime available, support may be conditioned by regime.

## 6. Abstention

Candidate states:

```text
SUPPORTED
BOUNDARY
OOD_ABSTAIN
```

Thresholds are selected on validation data, never test data.

## 7. Metrics

- OOD AUROC/AUPRC;
- confident-error rate;
- retained-set error versus coverage;
- calibration before/after abstention;
- latency overhead of the support mechanism.

A support mechanism is rejected if it adds labels without predicting actual failure/error risk.
