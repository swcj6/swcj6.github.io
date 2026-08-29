---
layout: post
title: Voxel-Based Composite RVE Simulation
description: MATLAB implementation of a voxel-based finite difference method for predicting the effective mechanical properties of particle-reinforced composites.

skills:
  - MATLAB
  - Computational Mechanics
  - Composite Materials
  - Representative Volume Elements
  - Finite Difference Methods
  - Material Homogenization
  - Voxel Simulation

main-image: /RVE-main.png
permalink: /projects/RVE-Composite/

github: https://github.com/swcj6/Calculating_Effective_Properties_via_RVEs

---

**Computational Mechanics Course Project**

## Overview

This project investigates the effective mechanical properties of particle-reinforced composite materials using a three-dimensional **Representative Volume Element (RVE)** and a voxel-based finite difference method.

The modeled composite consists of randomly distributed spherical **boron particles embedded in an aluminum matrix**.

Rather than explicitly modeling the complete microstructure of a large material sample, a smaller representative region is simulated and used to estimate the homogenized material properties of the composite.

The primary quantities calculated were the effective:

- Bulk modulus
- Shear modulus

The numerical framework was based on published voxel-computation methods developed by T. I. Zohdi.

My work focused on implementing key parts of the MATLAB simulation, generating randomized composite microstructures, computing stress and strain fields, and comparing the predicted effective properties with theoretical composite-material bounds.

---

## Project Context

The underlying RVE methodology and voxel-based numerical formulation were based on established research and course-provided algorithms rather than a numerical method originally developed by me.

My contribution focused on implementing and evaluating the computational framework.

I completed MATLAB functions for:

- Random particle placement
- Boundary-condition enforcement
- Strain calculation
- Stress calculation
- Stress-divergence calculation

I then used the completed model to investigate how voxel resolution and particle volume fraction affect the predicted effective properties of the composite.

---

## Why Representative Volume Elements?

Composite materials contain complex microstructures that can include particles, fibers, pores, grains, and multiple material phases.

Modeling every microscopic feature inside a full engineering component would be computationally expensive.

Instead, a small region called a **Representative Volume Element** can be analyzed.

```text
Full Composite Material
        ↓
Select Representative Region
        ↓
Build RVE
        ↓
Apply Mechanical Loading
        ↓
Compute Stress & Strain
        ↓
Determine Effective Properties
        ↓
Homogenized Material Model
```

The goal is for the RVE to contain enough microstructural information that its average mechanical response represents the larger material.

---

## Composite Microstructure

The simulated material consisted of:

```text
Aluminum Matrix
      +
Spherical Boron Particles
      ↓
Particle-Reinforced Composite
```

Particles were randomly distributed throughout a cubic RVE.

The algorithm required every particle to satisfy two conditions:

1. The particle must remain completely inside the RVE.
2. The particle cannot overlap another particle.

This creates a randomized composite microstructure for each simulation.

![Composite RVE](/projects/RVE-Composite/RVE-main.png)

---

## Random Particle Placement

I implemented the `findParticleCenters` function to generate valid particle locations.

The algorithm begins by generating a random candidate particle center.

```text
Generate Candidate Center
        ↓
Inside RVE?
     ↙     ↘
   No       Yes
   ↓         ↓
 Reject   Check Existing Particles
                  ↓
              Overlap?
              ↙    ↘
            Yes     No
             ↓       ↓
           Reject   Accept
```

For every candidate, the distance to all previously accepted particle centers is checked.

If the distance between two centers is less than twice the particle radius, the candidate is rejected.

The process continues until the required particle volume fraction is achieved.

---

## Voxel Representation

Instead of constructing a conventional finite element mesh, the RVE is divided into uniform cubic cells called **voxels**.

Each voxel is assigned either the mechanical properties of the aluminum matrix or the boron particulate phase.

This regular grid allows spatial derivatives to be calculated directly using finite differences.

---

## Why Use Voxels?

Traditional finite element analysis of complex microstructures can require:

```text
Geometry Construction
        ↓
Conforming Mesh Generation
        ↓
Element Mapping
        ↓
Numerical Integration
        ↓
Global Stiffness Matrix
        ↓
Matrix Solution
```

The voxel method simplifies this process:

```text
Microstructure
        ↓
Voxel Grid
        ↓
Finite Difference Operations
        ↓
Stress / Strain Solution
```

Because the geometry is represented on a regular Cartesian grid, the method also has potential compatibility with microstructural data obtained directly from imaging technologies such as CT scans.

---

## Finite Difference Formulation

The numerical method approximates spatial derivatives using neighboring voxels.

For displacement component $$u_i$$, a central finite difference approximation is used:

$$
\frac{\partial u_i}{\partial x_j}
\approx
\frac{
u_i(x_j+\Delta x_j)
-
u_i(x_j-\Delta x_j)
}{
2\Delta x_j
}
$$

This central difference scheme provides a second-order approximation of the displacement gradient.

The gradient is then used to calculate strain.

---

## Strain Calculation

For small deformation, the strain tensor is:

$$
\epsilon_{ij}
=
\frac{1}{2}
\left(
u_{i,j}+u_{j,i}
\right)
$$

I implemented the `computeStrain` routine to calculate strain at each voxel using displacement values from neighboring voxels.

Conceptually:

```text
Neighboring Voxel Displacements
        ↓
Finite Difference Gradient
        ↓
Displacement Gradient
        ↓
Strain Tensor
```

---

## Stress Calculation

The constituents were modeled as isotropic, linear-elastic materials.

Stress was calculated using:

$$
\sigma_{ij}
=
\lambda \epsilon_{kk}\delta_{ij}
+
2\mu\epsilon_{ij}
$$

where:

- $$\lambda$$ is the first Lamé parameter
- $$\mu$$ is the shear modulus
- $$\epsilon$$ is the strain tensor
- $$\sigma$$ is the stress tensor

I implemented the `computeStress` function to calculate the local stress state from the computed strain field.

---

## Stress Divergence

Mechanical equilibrium requires:

$$
\nabla \cdot \boldsymbol{\sigma}=0
$$

The stress-divergence calculation therefore determines how close the current displacement field is to mechanical equilibrium.

I implemented `computeStressDiv` using voxel-based finite difference operations.

The implementation includes both:

- Voxel-Laplacian terms
- Voxel cross-derivative terms

These terms evaluate stress changes using the neighboring voxel arrangement.

---

## Boundary Conditions

The RVE was subjected to a prescribed uniform strain boundary condition:

$$
\mathbf{u}|_{\partial\Omega}
=
\bar{\boldsymbol{\epsilon}}\mathbf{x}
$$

This forces the boundary of the RVE to follow a known macroscopic strain state.

The resulting average stress response can then be used to determine the equivalent homogenized properties of the material.

I implemented the `applyBCs` function to enforce this displacement condition on the boundary voxels.

---

## Material Homogenization

Once the RVE reaches mechanical equilibrium, the local stress and strain fields are averaged.

Conceptually:

```text
Local Stress Field
        +
Local Strain Field
        ↓
Volume Averaging
        ↓
Effective Material Response
        ↓
Bulk & Shear Modulus
```

For the homogenized material:

$$
\langle \boldsymbol{\sigma} \rangle
=
\mathbf{E}^{*}
\langle \boldsymbol{\epsilon} \rangle
$$

where:

$$
\mathbf{E}^{*}
$$

represents the effective elastic properties of the composite.

---

## Effective Bulk Modulus

The bulk modulus describes resistance to volumetric deformation.

It is calculated from the spherical components of the average stress and strain.

$$
K^{*}
=
\frac{1}{3}
\frac{
\left\langle \frac{1}{3}\text{tr}(\sigma)\right\rangle
}{
\left\langle \frac{1}{3}\text{tr}(\epsilon)\right\rangle
}
$$

---

## Effective Shear Modulus

The shear modulus describes resistance to distortional deformation.

The effective value is calculated from the deviatoric components of the average stress and strain fields.

The combination of bulk and shear modulus provides a compact characterization of the effective isotropic elastic response of the composite.

---

## Dynamic Relaxation

The equilibrium displacement field was obtained using a dynamic relaxation approach.

Instead of solving a large matrix equation directly, the equilibrium problem is converted into an artificial dynamic system:

```text
Initial Displacement
        ↓
Calculate Stress
        ↓
Calculate Stress Divergence
        ↓
Update Voxel Motion
        ↓
Check Equilibrium
        ↓
Repeat
```

Artificial dynamics are used only as a computational strategy for finding the static equilibrium solution.

---

## Kinetic Damping

Kinetic damping was used to accelerate convergence toward equilibrium.

The total artificial kinetic energy of the voxel system is monitored during the simulation.

```text
Kinetic Energy Increasing
        ↓
Approaching Peak
        ↓
Peak Detected
        ↓
Reset Velocity
        ↓
Continue Relaxation
```

When a kinetic energy peak is detected, the displacement state near the peak is retained while the artificial velocity is reset.

This allows the solution to approach equilibrium efficiently without requiring a conventional global stiffness-matrix solve.

---

## Simulation Study

Two main parameters were investigated:

- Particle volume fraction
- Voxel resolution

For a target particle volume fraction of:

```text
22%
```

four voxel resolutions were evaluated:

| Voxels Per Side | Approximate Particle Count |
|---:|---:|
| 34 | 1 |
| 48 | 3 |
| 67 | 8 |
| 84 | 16 |

Each configuration was simulated three times using independently generated particle distributions.

An additional configuration used:

```text
25% particle volume fraction
48 voxels per side
```

with modified particle properties based on results from the preceding composite-material design project.

---

## RVE Resolution — 34 Voxels

The lowest-resolution model contained approximately one spherical particle.

![34 Voxel RVE](/projects/RVE-Composite/rve34.png)

The simulated average properties were approximately:

```text
Effective Bulk Modulus  ≈ 98.0 GPa
Effective Shear Modulus ≈ 39.9 GPa
```

This configuration provided the lowest computational resolution but still produced effective properties near the expected values.

---

## RVE Resolution — 48 Voxels

Increasing the resolution to 48 voxels per side allowed approximately three particles to be represented.

![48 Voxel RVE](/projects/RVE-Composite/rve48.png)

The higher voxel resolution provides a more detailed representation of both the microstructure and the local mechanical fields.

---

## RVE Resolution — 67 Voxels

At 67 voxels per side, approximately eight boron particles were represented.

![67 Voxel RVE](/projects/RVE-Composite/rve67.png)

The larger RVE contains a more representative distribution of particles and allows the influence of multiple inclusions on the local strain-energy field to be visualized.

---

## RVE Resolution — 84 Voxels

The highest-resolution 22% volume-fraction model contained approximately sixteen particles.

![84 Voxel RVE](/projects/RVE-Composite/rve84.png)

Increasing the resolution provides a richer representation of the randomized microstructure but also significantly increases the number of voxel calculations required.

---

## 25% Particle Volume Fraction

An additional simulation investigated a target particle volume fraction of:

```text
25%
```

using 48 voxels per side.

![25 Percent RVE](/projects/RVE-Composite/rve25.png)

Across three simulations, the average predicted properties were approximately:

```text
Effective Bulk Modulus  ≈ 97.6 GPa
Effective Shear Modulus ≈ 40.0 GPa
```

---

## Results

Across the tested voxel resolutions and volume-fraction configurations, the predicted homogenized properties remained near:

```text
Bulk Modulus  ≈ 98 GPa
Shear Modulus ≈ 40 GPa
```

The results were compared with theoretical composite-material predictions from the preceding project, including the **Hashin-Shtrikman bounds**.

The simulations produced effective properties consistent with the expected theoretical range.

---

## Resolution Trade-Off

Increasing the voxel resolution improves the representation of the microstructure:

```text
Higher Voxel Resolution
        ↓
More Detailed Geometry
        ↓
More Representative Microstructure
        ↓
More Computational Operations
```

The project therefore illustrates a common computational mechanics trade-off between:

**model fidelity** and **computational cost**.

---

## Key Takeaways

This project demonstrated:

- Representative Volume Element modeling
- Composite material homogenization
- Three-dimensional voxel discretization
- Random microstructure generation
- Central finite differences
- Strain calculation
- Stress calculation
- Stress-divergence calculation
- Dynamic relaxation
- Kinetic damping
- Effective bulk and shear modulus prediction
- Validation using theoretical composite-material bounds

The project showed how a relatively simple regular voxel grid can be used to model complex heterogeneous microstructures without requiring a traditional conforming finite element mesh.

---

## What I Learned

This project gave me experience translating a published computational mechanics methodology into a working numerical simulation.

One of the most important concepts I learned was how material behavior at the microscale can be connected to engineering properties at the macroscale through homogenization.

The project also strengthened my understanding of:

- Computational micromechanics
- Material homogenization
- Composite materials
- Numerical differentiation
- Iterative equilibrium solvers
- Three-dimensional scientific computing
- Model-resolution trade-offs

It also demonstrated how computational material testing can potentially reduce reliance on physical testing when evaluating new microstructured materials.

---

## Research Basis

The voxel-based methodology was based primarily on the work of **T. I. Zohdi**, particularly:

**"Rapid Voxel-Based Digital-Computation for Complex Microstructured Media."**

The underlying method converts a material microstructure into a regular voxel grid and uses digital finite-difference operations to evaluate the mechanical response.

The numerical formulation and RVE methodology were not developed by me. My contribution was implementing key portions of the computational framework, completing the required MATLAB functions, running the numerical studies, and analyzing the resulting effective material properties.

---

## Tools & Methods

**Programming:** MATLAB

**Computational Mechanics:** Representative Volume Elements, Material Homogenization

**Numerical Methods:** Central Finite Differences, Dynamic Relaxation, Kinetic Damping

**Materials:** Particle-Reinforced Composites, Aluminum Matrix, Boron Particles

**Analysis:** Effective Bulk Modulus, Effective Shear Modulus, Hashin-Shtrikman Bounds

---

## References

1. Taylor, M. and Steigmann, D. J., *A Two-Dimensional Peridynamic Model for Thin Plates*, Mathematics and Mechanics of Solids, Vol. 20, No. 8, pp. 998–1010, 2015. https://doi.org/10.1177/1081286513512925.
