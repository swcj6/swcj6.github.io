---
layout: post
title: Peridynamic Fracture Simulation
description: MATLAB implementation of peridynamic fracture mechanics for simulating crack initiation and propagation in thin membranes.

skills:
  - MATLAB
  - Peridynamics
  - Fracture Mechanics
  - Numerical Methods
  - Computational Mechanics
  - Explicit Integration
  - Implicit Integration

main-image: /fracture-main.png
permalink: /projects/Membrane-Fracture/

github: https://github.com/swcj6/Peridynamics

---

**Computational Mechanics Course Project**

## Overview

This project investigates the use of **peridynamics** to simulate crack initiation and propagation in a thin membrane subjected to tearing loads.

Traditional continuum mechanics formulations rely on spatial derivatives of the displacement field. When a crack forms, the displacement field becomes discontinuous, making fracture difficult to model without additional techniques.

Peridynamics instead describes material behavior through nonlocal interactions between material points. Because the governing equations are formulated using integral relationships rather than spatial derivatives, cracks can initiate and propagate naturally as material bonds fail.

For this course project, I implemented and evaluated a two-dimensional peridynamic membrane model in MATLAB and compared explicit and implicit time-integration methods under two different tearing conditions.

---

## Project Context

The underlying peridynamic theory and numerical formulation were based on established research.

My work focused on:

- Implementing the numerical algorithms in MATLAB
- Applying displacement and velocity boundary conditions
- Implementing explicit and implicit time integration
- Modeling bond failure and material damage
- Identifying tearing conditions that produced different crack paths
- Comparing the numerical behavior and computational cost of the two solvers
- Generating fracture animations and damage visualizations

The project was based on bond-based peridynamic theory and research on peridynamic models for membranes and thin structures.

---

## Why Peridynamics?

In conventional continuum mechanics, the governing equation of motion requires derivatives of the displacement field.

When a crack forms:

```text
Continuous Material
        ↓
Crack Initiation
        ↓
Displacement Discontinuity
        ↓
Spatial Derivatives Become Difficult
```

This creates challenges for conventional numerical methods.

Peridynamics takes a different approach.

Instead of using local differential equations, each material point interacts with neighboring points through finite-distance bonds.

```text
Material Point
      ↓
Neighboring Points Within Horizon
      ↓
Bond Forces
      ↓
Deformation
      ↓
Bond Failure
      ↓
Crack Propagation
```

When individual bonds exceed a critical stretch, they break irreversibly.

A collection of broken bonds naturally develops into a crack.

---

## Peridynamic Interaction Model

Each material point interacts with other points within a finite neighborhood known as the **horizon**.

The governing equation can be written conceptually as:

$$
\rho \ddot{\mathbf{u}}(\mathbf{X},t)
=
\int_{\mathcal{H}_{\mathbf{X}}}
\mathbf{f}(\boldsymbol{\eta},\boldsymbol{\xi})\,dV'
+
\rho \mathbf{b}
$$

where:

- $$\rho$$ is material density
- $$\mathbf{u}$$ is displacement
- $$\mathbf{f}$$ is the pairwise bond force
- $$\boldsymbol{\xi}$$ is the reference separation between material points
- $$\boldsymbol{\eta}$$ is the relative displacement
- $$\mathcal{H}_{\mathbf{X}}$$ is the horizon surrounding a material point

Unlike classical continuum mechanics, this formulation does not require spatial derivatives of the displacement field at the crack surface.

---

## Bond-Based Damage Model

Material failure is modeled through the stretch of the bonds connecting neighboring material points.

The bond stretch is defined as:

$$
\lambda = \frac{y}{|\xi|}
$$

where $$y$$ is the current distance between two bonded material points.

A bond remains intact while:

$$
\lambda \leq \lambda_0
$$

and fails when:

$$
\lambda > \lambda_0
$$

For this simulation, the critical stretch was:

```text
λ₀ = 1.4
```

Once a bond fails, it remains permanently broken.

This simple rule allows cracks to nucleate and propagate without requiring a predefined crack path.

---

## Computational Model

The simulation modeled a thin square membrane.

| Parameter | Value |
|---|---:|
| Membrane Length | 1.0 m |
| Membrane Width | 1.0 m |
| Thickness | 0.1 mm |
| Density | 1500 kg/m³ |
| Bond Stiffness | 1.404 × 10¹³ Pa/m⁵ |
| Node Spacing | 0.01 m |
| Critical Stretch | 1.4 |
| Horizon | 3.03 × node spacing |
| Total Simulation Time | 0.2 s |

The membrane was discretized into material points rather than conventional finite elements.

Each point interacts with neighboring material points located within its peridynamic horizon.

---

## Boundary Conditions

The two vertical sides of the membrane were fixed within approximately 6 cm of the boundaries.

A portion of the upper boundary was then assigned a prescribed tearing velocity.

By changing the direction and magnitude of this imposed velocity, different fracture paths could be generated.

---

## Numerical Implementation

I implemented and completed several MATLAB functions required for the simulation, including:

```text
Main Simulation
      ↓
Boundary Conditions
      ↓
Peridynamic Bond Forces
      ↓
Equation of Motion
      ↓
Bond Damage
      ↓
Position / Velocity Update
      ↓
Damage Visualization
```

The implementation included:

- Simulation parameter initialization
- Velocity boundary conditions
- Displacement boundary conditions
- Explicit equation-of-motion integration
- Implicit equation-of-motion integration
- Damage tracking
- Fracture visualization
- Animation generation

---

## Explicit Time Integration

The first solver used an explicit central-difference time-integration scheme.

At each time step:

```text
Calculate Bond Forces
        ↓
Sum Forces on Each Node
        ↓
Calculate Acceleration
        ↓
Update Velocity
        ↓
Update Position
        ↓
Calculate Bond Stretch
        ↓
Break Failed Bonds
        ↓
Update Damage
```

The explicit method has the advantage of relatively low computational cost per time step.

However, numerical stability requires a sufficiently small time step.

For this project, the explicit time step was approximately:

```text
Δt = 0.0002 s
```

---

## Implicit Time Integration

The second solver used an **implicit trapezoidal rule with adaptive time stepping**.

Because the future state appears inside the governing equations, the solution must be found iteratively at every time step.

Conceptually:

```text
Estimate Future Position
        ↓
Calculate Forces
        ↓
Update Estimate
        ↓
Check Convergence
      ↙     ↘
   No        Yes
   ↓          ↓
Iterate    Accept Step
              ↓
       Adjust Time Step
```

The adaptive algorithm modifies the time step depending on how quickly the nonlinear iteration converges.

This can improve numerical stability for stiff problems, but significantly increases computational cost.

---

## Fracture Case 1 — Straight Tear

One goal was to identify a tearing velocity that caused two cracks to propagate approximately parallel through the membrane.

The straight-tear case used approximately:

```text
Y velocity = -5 m/s
Z velocity =  5 m/s
```

The resulting crack propagated across the membrane and eventually separated the sheet into multiple pieces.

<video controls autoplay loop muted playsinline style="width: 100%; height: auto;">
  <source src="/projects/Membrane-Fracture/explicit-straight.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

---

## Fracture Case 2 — Pinch Tear

The second loading condition caused the two cracks to converge toward each other.

The pinch-tear case used approximately:

```text
X velocity = 3 m/s
Z velocity = 1.5 m/s
```

Instead of tearing a strip completely across the membrane, the cracks converged and detached only the moving section.

<video controls autoplay loop muted playsinline style="width: 100%; height: auto;">
  <source src="/projects/Membrane-Fracture/explicit-pinch.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

This behavior resembles the way thin packaging materials can tear inward and form a small detached piece rather than producing a clean straight tear.

---

## Explicit vs. Implicit Results

Both numerical methods produced very similar fracture patterns.

### Straight Tear

**Explicit**

<video controls muted playsinline preload="metadata" style="width: 100%; height: auto;">
  <source src="/projects/Membrane-Fracture/explicit-straight.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

**Implicit**

<video controls muted playsinline preload="metadata" style="width: 100%; height: auto;">
  <source src="/projects/Membrane-Fracture/implicit-straight.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

### Pinch Tear

**Explicit**

<video controls muted playsinline preload="metadata" style="width: 100%; height: auto;">
  <source src="/projects/Membrane-Fracture/explicit-pinch.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

**Implicit**

<video controls muted playsinline preload="metadata" style="width: 100%; height: auto;">
  <source src="/projects/Membrane-Fracture/implicit-pinch.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

The crack locations and overall damage behavior were similar between the explicit and implicit simulations.

---

## Solver Performance

Although both methods produced similar physical results, their computational cost was significantly different.

| Method | Simulation Time |
|---|---:|
| Explicit | 543.775 s |
| Implicit | 5817.524 s |

The implicit method required approximately:

$$
\frac{5817.524}{543.775} \approx 10.7
$$

times more computation time than the explicit solver.

This was one of the most important results of the project.

---

## Why Was the Implicit Solver Slower?

The explicit method updates the system directly at each time step:

```text
Current State
      ↓
Calculate Forces
      ↓
Update State
```

The implicit solver requires iterative convergence:

```text
Current State
      ↓
Guess Future State
      ↓
Calculate Forces
      ↓
Check Error
      ↓
Update Guess
      ↓
Repeat Until Converged
```

Although implicit integration can offer better stability characteristics, each time step requires substantially more computation.

For this fracture problem, the explicit method provided similar fracture behavior with significantly lower computational cost.

---

## Results

The simulations demonstrated several important behaviors.

### Straight Tear

The imposed velocity caused the cracks to propagate approximately parallel through the membrane, producing a complete tear.

### Pinch Tear

Changing the velocity direction caused the crack paths to converge, detaching only the moving section.

### Damage Localization

For both loading cases, damage remained concentrated around the crack boundaries.

### Numerical Agreement

Explicit and implicit integration produced similar fracture geometries.

### Computational Cost

The implicit solver required substantially more computation time than the explicit method.

---

## Key Takeaways

This project demonstrated:

- Peridynamic simulation of spontaneous crack propagation
- Nonlocal modeling of material interactions
- Bond-based material failure
- Mesh-free computational mechanics
- Explicit central-difference integration
- Implicit trapezoidal integration
- Adaptive time stepping
- Numerical convergence
- Fracture visualization
- Comparison of computational efficiency between numerical solvers

The results showed that peridynamics can model complex fracture behavior without prescribing a crack path or requiring special treatment when discontinuities form.

---

## What I Learned

This project gave me experience implementing a computational mechanics method directly from mathematical formulations and published research.

One of the most important lessons was the trade-off between **numerical stability and computational cost**.

Although implicit integration is often attractive for stiff numerical systems, its nonlinear iterations made it significantly more computationally expensive for this problem.

Because the explicit and implicit methods produced similar fracture patterns, the explicit method provided a much more computationally efficient solution for the membrane tearing simulations.

The project also improved my understanding of:

- Fracture mechanics
- Nonlocal continuum mechanics
- Numerical integration
- Adaptive time stepping
- Stability and convergence
- Scientific programming
- Computational visualization

---

## Research Basis

This course project implemented established peridynamic methods rather than proposing a new peridynamic theory.

The project was based on **bond-based peridynamics**, originally developed to address discontinuities such as crack initiation and growth, together with two-dimensional membrane approximations for thin structures.

The underlying research shows how peridynamic formulations replace the differential operators used in classical continuum mechanics with nonlocal integral interactions, allowing cracks to form and propagate naturally.

---

## Tools & Methods

**Programming:** MATLAB

**Computational Mechanics:** Peridynamics, Fracture Mechanics

**Numerical Methods:** Explicit Central Differences, Implicit Trapezoidal Integration

**Solver Techniques:** Adaptive Time Stepping, Fixed-Point Iteration

**Modeling:** Bond Failure, Critical Stretch, Nonlocal Material Interaction

**Visualization:** MATLAB Scatter3, Animation, Damage Mapping

---

## References

1. Taylor, M. and Steigmann, D. J., *A Two-Dimensional Peridynamic Model for Thin Plates*, Mathematics and Mechanics of Solids, Vol. 20, No. 8, pp. 998–1010, 2015. https://doi.org/10.1177/1081286513512925.
