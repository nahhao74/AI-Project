# Geometry, SO(3) and Frame Canonicalization Plan

## 1. Objective

Move known geometric structure out of the neural network so model capacity is spent on dynamics rather than coordinate conventions.

## 2. Internal rotation authority

The authoritative internal orientation is a rotation matrix $R_{WB}$ after explicit conversion from the dataset's quaternion/Euler convention.

Raw quaternion is not assumed unique because $q$ and $-q$ represent the same orientation.

Before temporal use:

1. normalize quaternion;
2. repair sign continuity using consecutive dot products;
3. convert to the declared frame convention;
4. derive $R_{WB}$.

## 3. Relative rotation target

Primary candidate:

```math
\Delta R_h=R_t^\top R_{t+h},
```

with tangent-space target:

```math
r_h=\log(\Delta R_h)\in\mathbb{R}^3.
```

Reconstruction:

```math
\hat R_{t+h}=R_t\exp(\hat r_h^\wedge).
```

This removes absolute orientation offset and avoids direct Euclidean regression on quaternion components.

## 4. Heading-relative translation

Let $R_H$ contain current yaw while preserving world vertical. Candidate translation representation:

```math
v_H=R_H^\top v_W,
```

```math
\Delta p_H(h)=R_H^\top(p_{t+h}-p_t).
```

The purpose is to remove arbitrary global yaw without rotating gravity away from the vertical axis as a full body-frame transformation would.

## 5. Gravity-context feature

Candidate:

```math
g_B=R_{WB}^\top e_z.
```

This gives the dynamics model roll/pitch relation to gravity without requiring absolute yaw.

## 6. Invariance/equivariance test

A complete flight is rotated around world vertical by angle $\alpha$. After canonicalization, equivalent physical states should map to equivalent features within numerical tolerance.

This test is mandatory before claiming representation efficiency.

## 7. Alternative representation

A 6D continuous rotation representation is retained as a challenger if $SO(3)$ log-map behavior near rotations close to $\pi$ becomes problematic. Representation selection is empirical, not ideological.
