---
layout: post
title: Particle Swarm UAV Region Mapping
description: MATLAB simulation of decentralized multi-UAV region mapping using attraction-repulsion interaction models for target acquisition, obstacle avoidance, and swarm coordination.

skills:
  - MATLAB
  - Swarm Particle
  - Autonomous Systems
  - Numerical Simulation

main-image: /particle-swarm-main.png
permalink: /projects/Particle-Swarm/

github: https://github.com/swcj6/Particle_Swarm_Region_Mapping

---

**Computational Mechanics Course Project**

## Overview

This project implements a decentralized particle swarm model for autonomous region mapping using multiple unmanned aerial vehicles (UAVs).

Each UAV is modeled as a point-mass agent whose motion is determined by three interactions:

1. **Member-to-target interaction** — guides UAVs toward unmapped targets.
2. **Member-to-obstacle interaction** — modifies UAV motion around obstacles.
3. **Member-to-member interaction** — regulates spacing and coordination within the swarm.

Rather than prescribing an individual trajectory for each UAV, coordinated motion emerges from these local interaction rules.

The mathematical framework was based on the multi-UAV swarm model developed by T. I. Zohdi. My work focused on implementing and completing the MATLAB simulation, evaluating the interaction model, and analyzing swarm performance across environments with different swarm sizes, target counts, and obstacle densities.

---

## What I Implemented

- Implemented member-to-target interaction calculations.
- Implemented member-to-obstacle interaction calculations.
- Implemented member-to-member swarm interactions.
- Combined the three interaction terms into a weighted UAV acceleration direction.
- Implemented semi-implicit Euler time integration for UAV motion.
- Added maximum-velocity constraints to maintain bounded UAV speeds.
- Implemented obstacle-impact detection and UAV deactivation.
- Simulated four UAV mapping environments with varying numbers of agents, targets, and obstacles.
- Evaluated performance using mapping or termination time and surviving UAV count.
- Visualized target acquisition and UAV survival throughout each simulation.

---

## Problem

The objective was to model a swarm of UAVs attempting to map a set of targets distributed throughout a three-dimensional environment while navigating around obstacles.

Each UAV must simultaneously respond to three sources of interaction:

```text
                    Targets
                       ↓
                Target Interaction
                       ↓

Other UAVs  ←  UAV / Agent  →  Other UAVs
                       ↑
               Swarm Interaction
                       ↑
                    Obstacles
                       ↑
              Obstacle Interaction
```

Unlike traditional path planning, no complete trajectory is assigned to an individual UAV.

Instead, at every simulation step, each UAV calculates its interactions with the surrounding targets, obstacles, and active swarm members.

These interactions are combined to determine its acceleration and subsequent trajectory.

---

## Decentralized Swarm Model

The UAVs are modeled as point masses governed by Newton's second law:

$$
m_i\ddot{\mathbf{r}}_i = \mathbf{\Psi}^{tot}_i
$$

where the total force acting on UAV $$i$$ depends on its interactions with:

$$
\mathbf{\Psi}^{tot}_i =
F
\left(
\mathbf{N}^{mt}_i,
\mathbf{N}^{mo}_i,
\mathbf{N}^{mm}_i
\right)
$$

where:

- $$\mathbf{N}^{mt}_i$$ represents member-to-target interaction.
- $$\mathbf{N}^{mo}_i$$ represents member-to-obstacle interaction.
- $$\mathbf{N}^{mm}_i$$ represents member-to-member interaction.

Because every UAV follows the same interaction model and no leader controls the swarm, the system operates using decentralized behavior.

---

## Member-to-Target Interaction

For UAV $$i$$ and target $$j$$, the interaction begins by calculating their separation distance:

$$
d_{ij}^{mt}
=
\left\|
\mathbf{r}_i-\mathbf{T}_j
\right\|
$$

A unit direction vector is then calculated from the UAV toward the target.

The weighted target interaction is modeled using competing exponential terms:

$$
\hat{\mathbf{n}}_{i\rightarrow j}
=
\left(
w_{t1}e^{-a_1d_{ij}^{mt}}
-
w_{t2}e^{-a_2d_{ij}^{mt}}
\right)
\mathbf{n}_{i\rightarrow j}
$$

where:

- $$w_{t1}$$ and $$w_{t2}$$ control interaction strength.
- $$a_1$$ and $$a_2$$ control exponential decay.
- $$d_{ij}^{mt}$$ is the UAV-to-target distance.

The contributions from all active targets are summed:

$$
\mathbf{N}^{mt}_i
=
\sum_{j=1}^{N_t}
\hat{\mathbf{n}}_{i\rightarrow j}
$$

When a UAV moves within the specified target-arrival tolerance, the target is considered mapped and removed from the active target set.

---

## Member-to-Obstacle Interaction

Obstacle interactions use the same general attraction-repulsion structure.

For an obstacle $$j$$:

$$
\hat{\mathbf{n}}_{i\rightarrow j}
=
\left(
w_{o1}e^{-b_1d_{ij}^{mo}}
-
w_{o2}e^{-b_2d_{ij}^{mo}}
\right)
\mathbf{n}_{i\rightarrow j}
$$

The obstacle contributions are summed to obtain:

$$
\mathbf{N}^{mo}_i
=
\sum_{j=1}^{N_o}
\hat{\mathbf{n}}_{i\rightarrow j}
$$

The parameters control how strongly the UAV responds as its distance from an obstacle changes.

If a UAV enters the obstacle-impact tolerance,

$$
d_{ij}^{mo} \leq \mathrm{tol}_{mo}
$$

the UAV is marked inactive and removed from the active swarm.

---

## Member-to-Member Interaction

Each active UAV also interacts with the other active members of the swarm.

$$
\hat{\mathbf{n}}_{i\rightarrow j}
=
\left(
w_{m1}e^{-c_1d_{ij}^{mm}}
-
w_{m2}e^{-c_2d_{ij}^{mm}}
\right)
\mathbf{n}_{i\rightarrow j}
$$

The total member interaction becomes:

$$
\mathbf{N}^{mm}_i
=
\sum_{\substack{j=1 \\ j\neq i}}^{N_m}
\hat{\mathbf{n}}_{i\rightarrow j}
$$

The competing attractive and repulsive components allow the swarm to maintain group interaction without forcing all UAVs toward the same position.

---

## Combined Swarm Behavior

The three interaction terms are combined using global weighting parameters:

$$
\mathbf{N}^{tot}_i
=
W_{mt}\mathbf{N}^{mt}_i
+
W_{mo}\mathbf{N}^{mo}_i
+
W_{mm}\mathbf{N}^{mm}_i
$$

The resulting direction is normalized:

$$
\mathbf{n}^{*}_i
=
\frac{\mathbf{N}^{tot}_i}
{\left\|\mathbf{N}^{tot}_i\right\|}
$$

The normalized direction is then multiplied by the available thrust to determine the UAV's acceleration.

```text
Target Interaction
        +
Obstacle Interaction
        +
Member Interaction
        ↓
Weighted Interaction Vector
        ↓
Normalize Direction
        ↓
Apply UAV Thrust
        ↓
Acceleration
```

Changing the interaction weights changes the collective behavior of the swarm without requiring explicit trajectories for individual UAVs.

---

## Numerical Integration

UAV motion is advanced in time using **semi-implicit Euler integration**.

Velocity is first updated:

$$
\mathbf{v}_{n+1}
=
\mathbf{v}_n
+
\mathbf{a}_n\Delta t
$$

The updated velocity is then used to calculate the new position:

$$
\mathbf{x}_{n+1}
=
\mathbf{x}_n
+
\mathbf{v}_{n+1}\Delta t
$$

A maximum speed constraint is also imposed:

$$
\|\mathbf{v}\| > v_{\max}
$$

If the maximum is exceeded, the velocity is rescaled while preserving its direction:

$$
\mathbf{v}
\leftarrow
v_{\max}
\frac{\mathbf{v}}
{\|\mathbf{v}\|}
$$

This keeps UAV speeds within the prescribed simulation limit.

---

## Simulation Study

Four environments were evaluated to study the effect of swarm size, target count, and obstacle density.

| Scenario | UAVs $$N_m$$ | Targets $$N_t$$ | Obstacles $$N_o$$ |
|---|---:|---:|---:|
| 1 | 100 | 100 | 0 |
| 2 | 100 | 100 | 100 |
| 3 | 100 | 5 | 500 |
| 4 | 5 | 2 | 10,000 |

Each configuration was simulated five times.

---

## Scenario 1 — Open Environment

**100 UAVs · 100 Targets · 0 Obstacles**

![Particle Swarm Open Environment](/projects/Particle-Swarm/scenario1.png)

With no obstacles present, the swarm can spread directly through the environment toward the distributed targets.

Across five trials:

- **Average mapping time:** 8.30 s
- **Average surviving UAVs:** 100 / 100
- **Survival rate:** 100%

This scenario provides a baseline for evaluating the effect of obstacles in subsequent simulations.

---

## Scenario 2 — Moderate Obstacle Environment

**100 UAVs · 100 Targets · 100 Obstacles**

![Particle Swarm Moderate Obstacles](/projects/Particle-Swarm/scenario2.png)

The addition of obstacles forces each UAV to simultaneously balance target acquisition, obstacle interaction, and swarm coordination.

Across five trials:

- **Average mapping time:** 8.24 s
- **Average surviving UAVs:** 62.6 / 100
- **Average survival rate:** 62.6%

Although the target-completion time remained similar to the open-environment case, UAV attrition increased significantly.

This demonstrates why completion time alone is not sufficient for evaluating swarm performance.

---

## Scenario 3 — Dense Obstacle Environment

**100 UAVs · 5 Targets · 500 Obstacles**

![Particle Swarm Dense Obstacles](/projects/Particle-Swarm/scenario3.png)

This scenario reduces the target count while substantially increasing obstacle density.

Across five trials:

- **Average mapping time:** 6.23 s
- **Average surviving UAVs:** 84.2 / 100
- **Average survival rate:** 84.2%

Of the successful mapping configurations tested, this case produced the lowest average target-completion time.

Because only five targets needed to be reached by a 100-member swarm, significant swarm redundancy remained even in the obstacle-dense environment.

---

## Scenario 4 — Extreme Obstacle Density

**5 UAVs · 2 Targets · 10,000 Obstacles**

![Particle Swarm Extreme Obstacles](/projects/Particle-Swarm/scenario4.png)

The final case places a very small swarm in an extremely obstacle-dense environment.

Across five trials:

- **Average termination time:** 3.04 s
- **Average surviving UAVs:** 0 / 5
- **Survival rate:** 0%

The short simulation time does **not** represent improved mapping performance.

Instead, the simulation terminated rapidly because all active UAVs were lost to obstacle impacts.

This case demonstrates a failure regime in which the available swarm size is insufficient for the imposed obstacle density.

---

## Results

| Configuration | Avg. Time | Avg. UAVs Remaining | Survival |
|---|---:|---:|---:|
| 100 UAVs / 100 Targets / 0 Obstacles | 8.30 s | 100.0 | 100% |
| 100 UAVs / 100 Targets / 100 Obstacles | 8.24 s | 62.6 | 62.6% |
| 100 UAVs / 5 Targets / 500 Obstacles | 6.23 s | 84.2 | 84.2% |
| 5 UAVs / 2 Targets / 10,000 Obstacles | 3.04 s* | 0.0 | 0% |

\*Scenario 4 reports termination time rather than successful mapping time because the swarm was completely deactivated.

The results show that **mapping time and swarm survival must be considered together** when evaluating swarm performance.

For example, Scenario 2 completed in approximately the same time as Scenario 1, but only 62.6% of the UAVs survived on average.

Scenario 4 terminated fastest, but the swarm experienced complete UAV attrition.

---

## Emergent Swarm Behavior

A key feature of the model is that global swarm coordination is not directly prescribed.

Each UAV independently evaluates:

```text
Where are the remaining targets?

Where are the obstacles?

Where are the other active UAVs?
```

The combination of these local calculations produces the global swarm trajectory.

During the simulations, UAVs spread from their initially clustered region and move toward different remaining targets.

For the larger swarm simulations, the largest reduction in unmapped targets generally occurred during the middle portion of the simulation as the UAVs became more spatially distributed.

This demonstrates how relatively simple decentralized interaction laws can produce complex collective behavior.

---

## Engineering Takeaways

This project strengthened my understanding of how mathematical interaction models can be translated into a working multi-agent simulation.

Key takeaways included:

- Implementing decentralized swarm dynamics from mathematical equations.
- Translating attraction-repulsion interaction laws into MATLAB.
- Applying Newtonian mechanics to multi-agent UAV simulation.
- Using semi-implicit numerical integration for dynamic systems.
- Implementing collision detection and agent deactivation.
- Evaluating swarm performance using multiple metrics rather than completion time alone.
- Understanding how swarm size, target density, obstacle density, and interaction weights influence emergent behavior.

The project also highlighted an important limitation of decentralized interaction models: parameter choices that perform well in one environment may perform poorly when the environment or swarm configuration changes substantially.

---

## Tools & Methods

**Programming:** MATLAB

**Swarm Modeling:** Attraction-Repulsion Interactions

**Dynamics:** Newtonian Point-Mass Model

**Numerical Methods:** Semi-Implicit Euler Integration

**Autonomy:** Target Acquisition, Obstacle Interaction, Swarm Coordination

**Analysis:** Mapping Time, UAV Survival, Parametric Simulation

---

## References

1. Zohdi, T. I., *The Game of Drones: Rapid Agent-Based Machine-Learning Models for Multi-UAV Path Planning*, Computational Mechanics, 2019.
