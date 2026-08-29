---
layout: post
title: PINN Topology Optimization for Additive Manufacturing
description: Physics-informed neural topology optimization with additive manufacturing minimum feature-size constraints.

skills:
  - Physics-Informed Neural Networks
  - Topology Optimization
  - PyTorch
  - Fourier Features
  - Additive Manufacturing
  - Python

main-image: /pinn_final.png
permalink: /projects/PINN/

github: https://github.com/swcj6/Fourier_TOPINN

---

## Overview

This research investigates a machine learning-driven topology optimization framework for generating lightweight structural designs while incorporating additive manufacturing constraints.

Conventional topology optimization can produce thin members, narrow gaps, and small geometric features that may be difficult or impossible to manufacture. To address this limitation, I developed a neural topology optimization framework that represents both structural displacement and material density as continuous neural fields.

The primary focus of the project was enforcing **minimum feature size** using Fourier-based length-scale control so that optimized structures remain mechanically efficient while becoming more suitable for additive manufacturing.

---

## My Contributions

- Developed a two-network neural topology optimization framework in PyTorch.
- Implemented a displacement network to approximate the structural displacement field.
- Implemented a density network to represent the material distribution as a continuous function.
- Formulated structural mechanics using total potential energy and automatic differentiation.
- Implemented volume-constrained topology optimization using alternating neural-network updates.
- Investigated Fourier-based spatial-frequency control for enforcing additive manufacturing minimum feature size.
- Compared baseline and length-controlled optimized structures.

---

## Problem Definition

Topology optimization seeks the optimal distribution of material within a design domain while satisfying structural and material constraints.

For this project, the objective was to generate a stiff structure using only a prescribed amount of material.

| Parameter | Value |
|---|---|
| Design Domain | 80 × 40 |
| Volume Fraction | 40% |
| Young's Modulus | 1.0 |
| Poisson's Ratio | 0.3 |
| Boundary Condition | Left edge fixed |
| Loading | Downward point load at bottom-right |

---

## Neural Topology Optimization Framework

The framework uses two neural networks:

### Displacement Network — Disp_Net

The displacement network approximates the structural displacement field:

$$
\mathbf{u}(x,y) =
\begin{bmatrix}
u_x \\
u_y
\end{bmatrix}
$$

The network architecture is:

```text
Input: (x, y)

2
↓
64
↓
64
↓
64
↓
2

Output: (ux, uy)
```

The hidden layers use **Tanh** activation functions.

A smooth boundary-condition mask is applied to enforce zero displacement along the fixed edge of the cantilever beam.

---

### Density Network — TO_Net

The second neural network represents the material density field:

$$
\rho(x,y) \in [0,1]
$$

where:

```text
ρ ≈ 1  → Solid material
ρ ≈ 0  → Void
```

Instead of assigning an independent design variable to every finite element, the material distribution is represented as a continuous neural function.

This allows the density field to be evaluated at arbitrary spatial coordinates and resolutions.

---

## Physics-Based Optimization

Structural mechanics are incorporated through the total potential energy:

$$
\Pi = U_{int} - W_{ext}
$$

where:

- $$U_{int}$$ is the internal strain energy.
- $$W_{ext}$$ is the external work.

Automatic differentiation is used to calculate displacement gradients and strain terms directly from the neural displacement field.

Material stiffness is penalized using a SIMP-style relationship:

$$
E(\rho) \propto \rho^p
$$

with:

$$
p = 3
$$

This penalization encourages the optimized density field toward solid and void regions.

---

## Training Strategy

Optimization is divided into two phases.

### Phase 1 — Mechanical Pretraining

The displacement network is first trained on a fully solid domain:

```text
ρ = 1
```

for approximately:

```text
1000 epochs
```

This allows the displacement network to learn the structural response before topology optimization begins.

---

### Phase 2 — Alternating Optimization

The displacement and topology networks are then optimized alternately for approximately:

```text
1500 outer iterations
```

Each topology iteration contains:

```text
50 displacement-network updates
1 density-network update
```

The displacement network therefore continuously adapts to the changing structural topology.

---

## Additive Manufacturing Challenge

A mathematically optimized topology is not always manufacturable.

Unconstrained topology optimization may generate:

- Extremely thin structural members
- Small voids
- Narrow gaps
- High-frequency boundaries
- Fragile geometric details

These features may fall below the resolution limits of an additive manufacturing process.

The primary manufacturing constraint investigated in this project was therefore **minimum feature size**.

---

## Fourier-Based Minimum Feature Size Control

The density network uses Fourier features to control the spatial frequencies that can appear in the optimized material field.

Spatial frequency is directly related to geometric feature size:

```text
High spatial frequency
        ↓
Small geometric features

Low spatial frequency
        ↓
Larger geometric features
```

By restricting high-frequency components, the topology optimization process can suppress features that are smaller than the desired manufacturing resolution.

Conceptually:

$$
l_{min} \uparrow
\quad \Rightarrow \quad
f_{max} \downarrow
\quad \Rightarrow \quad
\text{Larger structural features}
$$

This provides a tunable mechanism for controlling minimum printable feature size.

---

## Fourier Feature Network

The density network maps the spatial coordinates through a Fourier feature layer before passing them into the neural network.

```text
(x, y)
   ↓
Fourier Feature Mapping
   ↓
cos(πBx), sin(πBx)
   ↓
64
   ↓
64
   ↓
Density ρ
```

The Fourier representation limits the spatial frequencies available to the network and therefore controls the geometric resolution of the optimized structure.

---

## Results

Three configurations were compared.

### Baseline — No Explicit Length Control

The baseline topology does not explicitly restrict minimum geometric feature size.

![Baseline Topology](/projects/PINN/baseline.png)

The resulting density field can develop smaller geometric features without direct manufacturing control.

---

### Minimum Feature Size — lmin = 10

The first Fourier-controlled configuration used:

```text
lmin = 10
```

![Minimum Feature Size 10](/projects/PINN/lmin10.png)

This configuration allows relatively fine structural detail while suppressing some high-frequency geometry.

---

### Minimum Feature Size — lmin = 20

The second configuration used:

```text
lmin = 20
```

![Minimum Feature Size 20](/projects/PINN/pinn_final.png)

Increasing the minimum length scale produces thicker structural members and suppresses smaller geometric features.

---

## Topology Comparison

| Configuration | Geometric Behavior |
|---|---|
| Baseline | No explicit minimum feature-size control |
| $$l_{min}=10$$ | Finer structural members |
| $$l_{min}=20$$ | Thicker members and fewer small-scale artifacts |

The results demonstrate a direct relationship between the Fourier length-scale parameter and the geometric complexity of the optimized topology.

Increasing $$l_{min}$$ produces larger and more manufacturable structural features, with the expected trade-off of reduced fine-scale geometric freedom.

---

## Current Development — Overhang Constraint

I am also extending the framework beyond minimum feature size to address additive manufacturing overhang constraints.

The current implementation uses a layer-by-layer support filter inspired by additive manufacturing topology optimization methods.

For each layer, printable material is constrained by the amount of supporting material available in the previous layer.

Conceptually:

```text
Raw Neural Density
        ↓
Layer-by-Layer Support Check
        ↓
Printable Density
        ↓
Structural Optimization
```

A nominal overhang angle of approximately:

```text
45°
```

is currently used in the implementation.

This allows the optimization process to consider not only the size of structural features, but also whether those features can be physically supported during additive manufacturing.

---

## Why This Matters

A common topology optimization workflow is:

```text
Topology Optimization
        ↓
Mathematically Optimal Geometry
        ↓
Manufacturing Problems
        ↓
Manual Redesign
        ↓
Fabrication
```

The goal of this research is to move toward:

```text
Topology Optimization
        +
Manufacturing Constraints
        ↓
Manufacturable Optimized Geometry
        ↓
Fabrication
```

Embedding manufacturing constraints directly into the optimization process can reduce redesign effort and improve the connection between computational optimization and physical manufacturing.

---

## Key Takeaways

This project demonstrates:

- Continuous neural representations of structural displacement and material density.
- Mechanics-based optimization using automatic differentiation.
- Alternating optimization of structural response and material distribution.
- Fourier-based control of minimum structural feature size.
- Integration of additive manufacturing constraints into neural topology optimization.
- A pathway toward combining minimum feature size and overhang constraints in a unified optimization framework.

---

## Research Poster

This research was presented as:

**Machine Learning-Driven Topology Optimization Considering the Restrictions of Additive Manufacturing: Minimum Feature Size**

The work was conducted in the **GIFTED Lab at Santa Clara University under Dr. Jun Wang** and supported by the **Kuehler Undergraduate Research Fellowship / Santa Clara University funding**.

![Research Poster](/projects/PINN/research-poster.jpg)

---

## Tools & Methods

**Programming:** Python, PyTorch

**Machine Learning:** Physics-Informed Neural Networks, Fourier Features

**Optimization:** Topology Optimization, SIMP-style Penalization

**Mechanics:** Linear Elasticity, Automatic Differentiation, Total Potential Energy
