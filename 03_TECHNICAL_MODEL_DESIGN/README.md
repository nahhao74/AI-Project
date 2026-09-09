# Step 3 — Technical Model Design

**Status:** `NOT_STARTED_PENDING_EMPIRICAL_ENTRY_GATES`  
**Purpose:** Step 3 converts Step 2 methodology into exact implementable model/data specifications **after** the required data and benchmark evidence exists.

## 1. Why Step 3 is not started yet

Step 3 must not freeze technical parameters from literature alone. At minimum, the project first needs:

```text
EXP-000  source/schema/signal-semantics audit
EXP-010  public benchmark reproduction
```

and, before freezing most temporal/representation parameters:

```text
EXP-100  geometry representation audit
EXP-105  structured low-rank audit
EXP-110  timing/spectral audit
EXP-115  time-frequency transient audit
```

Without these results, values such as sample rate, filter cutoff, history length, latent dimension, VARX order, Koopman rank, TCN receptive field or Mamba state size are only guesses.

## 2. Entry gates

### GATE-S3-01 — Data semantics

`EXP-000` must establish for the first active dataset:

- source identity/hash;
- flight/session identity;
- timestamp semantics;
- signal units and coordinate frames;
- quaternion convention;
- command versus actual-actuator semantics;
- missing/derived/filtering provenance;
- runtime availability class.

Unknown critical semantics block Step 3 for that signal.

### GATE-S3-02 — Reproducible baseline

`EXP-010` must reproduce at least one official/reference real-flight baseline within a prospectively defined tolerance. Failure to reproduce is an engineering blocker, not evidence that a new model is better.

### GATE-S3-03 — Data-supported representation

Before the final input contract is frozen, representation experiments must determine whether heading-relative/$SO(3)$ canonicalization, actuator features or optional compression are retained.

### GATE-S3-04 — Data-supported memory/order

Before temporal-model dimensions are frozen, Step 2 diagnostics must provide evidence for:

- useful bandwidth;
- actuator/effect delay;
- temporal correlation/memory;
- effective Hankel/subspace order;
- residual memory after explicit modeling.

## 3. Planned Step 3 authorities

When entry gates pass, Step 3 will contain:

```text
01_INPUT_OUTPUT_CONTRACT.md
02_DATA_PIPELINE_SPECIFICATION.md
03_FEATURE_REPRESENTATION_SPECIFICATION.md
04_WORLD_MODEL_ARCHITECTURE.md
05_TRAINING_PIPELINE.md
06_INFERENCE_PIPELINE.md
07_RUNTIME_AND_LATENCY_SPECIFICATION.md
08_UNCERTAINTY_AND_ABSTENTION_SPECIFICATION.md
09_MODEL_COMPLEXITY_AND_DEPLOYMENT_SPECIFICATION.md
```

Detailed algorithm authorities will be created only for mechanisms that remain `READY_TO_TEST` after Step 2 activation policy.

## 4. What Step 3 will freeze

Examples of decisions that belong to Step 3, not Step 2:

```text
canonical sample rate
exact input fields and masks
history length/receptive field
prediction horizons
filter design and coefficients
normalization parameters/contract
rotation representation
actuator-model structure/order
effective dynamic state order
VARX/TLS/N4SID configuration
SINDy library and sparsity rule
Koopman lifting/rank if retained
MLP/ESN/GRU/TCN/Mamba exact architecture
loss functions and weights
optimizer/training schedule
uncertainty/calibration method
OOD/support thresholds
runtime graph and target hardware budget
```

Every frozen value requires a parameter decision record containing its evidence and experiment identity.

## 5. Relationship to Step 4

Step 3 specifies **how the selected candidates are implemented**. Step 4 stores the evidence proving whether those choices worked.

```text
Step 1: WHY
Step 2: HOW TO TEST / DECIDE
EXP-000+ : EMPIRICAL EVIDENCE
Step 3: EXACT IMPLEMENTATION
Step 4: RESULTS / CLAIMS
```

## 6. Current state

```text
STEP_3_TECHNICAL_MODEL_DESIGN = NOT_STARTED
ENTRY_CRITERIA                = DEFINED
EXP_000                        = NOT_EXECUTED
EXP_010                        = NOT_EXECUTED
FINAL_INPUT_CONTRACT           = NOT_FROZEN
FINAL_ARCHITECTURE             = NOT_SELECTED
```
