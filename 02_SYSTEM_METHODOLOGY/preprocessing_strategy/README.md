# Preprocessing Strategy

Step 2 treats preprocessing as a scientific module rather than a utility script. Every transform must document the physical assumption it introduces, whether it is causal at runtime, and its compute/latency cost.

## Pipeline

```text
source timestamps / semantics
→ causal alignment and missing-data policy
→ frame and unit normalization
→ quaternion/SO(3) canonicalization
→ causal filtering if justified
→ physics-lite features
→ train-only statistical conditioning
→ temporal window construction
```

Detailed authorities:

- `TIME_SYNCHRONIZATION_AND_CAUSALITY.md`
- `GEOMETRY_SO3_AND_FRAMES.md`
- `FILTERING_SPECTRAL_AND_STATISTICAL_PLAN.md`
- `DATA_AUGMENTATION_PLAN.md`

## Key rules

1. Never shift away physical command→actuator/effect delay merely to maximize correlation.
2. Runtime features are causal; zero-phase filtering is offline-only.
3. Geometry canonicalization precedes statistical compression.
4. Standardization is the baseline; whitening/PCA require ablation evidence.
5. FFT/Welch/coherence are offline design diagnostics unless runtime benefit is separately demonstrated.
6. Data augmentation must preserve physical and timing semantics.
7. All fitted transforms are train-only and versioned.

## Parameter policy

No cutoff, history length, whitening rank, PCA dimension or spectral window is considered final in Step 2. Data diagnostics define candidate ranges; validation selects; test evaluates only after freeze.
