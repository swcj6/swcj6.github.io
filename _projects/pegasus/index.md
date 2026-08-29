---
layout: post
title: Pegasus Autonomous Hexacopter
description: Structural design and development of an autonomous wildfire-response hexacopter for the Vertical Flight Society Design-Build-Vertical Flight competition.
skills:
  - SOLIDWORKS
  - Finite Element Analysis
  - Topology Optimization
  - Structural Design
  - Design for Manufacturing
  - CNC Machining
  - Additive Manufacturing
main-image: /Pegasus.jpg
permalink: /projects/pegasus/
---

## Overview

Pegasus is an autonomous electric vertical takeoff and landing aircraft developed for the Vertical Flight Society Design-Build-Vertical Flight competition. The platform was designed around early-stage wildfire response, including aerial reconnaissance, autonomous mission execution, payload retrieval, and lightweight supply delivery.

I worked as part of a six-person mechanical engineering senior design team over a seven-month development cycle, with primary responsibility for the aircraft's structural design, structural analysis, weight reduction, and manufacturability.

The final aircraft used a six-motor hexacopter configuration with a modular aluminum airframe, independent propulsion and avionics power systems, a Cube Orange+ flight controller, and a bottom-mounted payload mechanism.

{% include image-gallery.html images="Isometric_view.png" height="500" %}

---

## My Contributions

- Worked on the structural design of the autonomous hexacopter, including the central airframe and propulsion arms.
- Designed the **6061-T6 aluminum airframe and motor arms in SOLIDWORKS** using iterative finite element analysis and topology optimization to reduce structural mass while maintaining required strength and stiffness.
- Applied design-for-manufacturing principles to components fabricated using **CNC machining, waterjet cutting, laser cutting, and additive manufacturing**.
- Designed structural components around available manufacturing capabilities to allow rapid iteration and in-house fabrication.

---

## Design Requirements

The aircraft was designed around both competition requirements and the operational constraints associated with wildfire response.

Key structural and system requirements included:

- Maximum takeoff weight below **25 lb**
- Thrust-to-weight ratio greater than **2.0**
- Mission endurance greater than **10 minutes**
- Structural factor of safety greater than or equal to **2.0**
- Maximum structural deflection below **1 mm**
- Stable autonomous hover capability
- Modular payload integration
- Rapid field deployment
- Manufacturable structural components
- Adequate separation between rotor excitation frequencies and structural natural frequencies

---

## Structural Design

The airframe serves as the primary structural backbone of the aircraft and supports the propulsion, avionics, payload, and landing systems.

I designed the aircraft around a modular hexacopter architecture consisting of a reinforced central frame and six aluminum propulsion arms.

**6061-T6 aluminum** was selected as the primary structural material because of its balance of:

- Strength-to-weight ratio
- Stiffness
- Thermal durability
- Machinability
- Cost
- Availability
- Compatibility with in-house manufacturing equipment

The central frame was designed to provide efficient load paths between the propulsion arms, avionics, landing gear, and bottom-mounted payload system while maintaining a relatively low center of gravity.

---

## Topology Optimization

Weight reduction was a major design objective because aircraft endurance and hover efficiency are highly sensitive to total mass.

I used an iterative structural optimization process consisting of:

1. Baseline CAD geometry
2. Finite element analysis
3. Identification of low-stress regions
4. Topology optimization
5. Manual geometry refinement
6. Structural revalidation
7. Manufacturability review

This process was repeated until the structures converged on lightweight geometries that maintained the required factors of safety and deflection limits.

### Top Plate

The top structural plate went through multiple iterations of topology optimization and manual redesign.

The final design achieved approximately a **54.6% mass reduction** compared with the original geometry.

### Bottom Plate

The bottom plate served as both a structural element and an integration platform for the payload system and landing gear.

The optimized design achieved approximately a **57.2% mass reduction** while preserving structural integrity and mounting interfaces.

{% include image-gallery.html images="plate-optimization.png" height="400" %}

---

## Propulsion Arm Design

The six propulsion arms transfer thrust loads from the motors into the central frame.

Each arm was manufactured from **1.5-inch square 6061-T6 aluminum tubing with a 1/16-inch wall thickness**.

Square tubing was selected instead of round tubing because the flat surfaces simplified:

- Motor alignment
- CNC machining
- Bolt-hole placement
- Central-frame attachment
- Assembly and repeatability

The arms were approximately **18 inches long** to provide sufficient propeller clearance and aircraft stability.

---

## Propulsion Arm Optimization

Topology optimization was also applied to the propulsion arms.

Material was removed primarily from low-stress regions near the neutral axis while preserving material around motor mounts, frame interfaces, and major load paths.

The optimization reduced the mass of each arm from approximately:

- **Initial mass:** 0.63 lb
- **Final mass:** 0.42 lb
- **Mass reduction:** 33.3%

The optimized arm retained a calculated factor of safety significantly above the minimum structural requirement.

{% include image-gallery.html images="arm-optimization.png" height="450" %}

---

## Finite Element Analysis

Finite element analysis was used throughout the design process to evaluate stress, deformation, and structural safety.

Static structural simulations were performed on:

- Top central plate
- Bottom central plate
- Propulsion arms
- Major structural interfaces

The analysis focused on two primary constraints:

- **Factor of safety greater than 2.0**
- **Maximum deflection below 1 mm**

The final aircraft design achieved a maximum structural deflection of approximately **0.207 mm** and a minimum factor of safety of approximately **2.04**.

### Central Airframe Analysis

{% include image-gallery.html images="von-mises-top-plate.jpg, von-mises-bottom-plate.jpg" height="400" %}

### Propulsion Arm Analysis

{% include image-gallery.html images="von-mises-arm.jpg" height="450" %}

---

## Modal Analysis

Modal analysis was performed to investigate potential vibration and resonance issues caused by the six rotating propulsion systems.

The goal was to maintain adequate separation between structural natural frequencies and motor/propeller excitation frequencies.

The first structural natural frequency of the final aircraft was approximately **89.985 Hz**.

The modal analysis was used to help establish conservative operating limits during testing and reduce the risk of sustained operation near resonance.

{% include image-gallery.html images="Arm_Frequency_Analysis_(Mode_Shape_1).png" height="450" %}

---

## Design for Manufacturing

The structural design was optimized not only for performance, but also for realistic fabrication.

I designed components around manufacturing processes available through the university machine shop and prototyping facilities.

Manufacturing methods included:

- 3-axis CNC machining
- Waterjet cutting
- Laser cutting
- Drilling and manual machining
- Additive manufacturing

The propulsion arms were manufactured using three-axis CNC milling, allowing precise control over motor mounting features and airframe interface holes.

Additively manufactured components were used where rapid iteration, complex geometry, or lower structural loads made polymer components appropriate.

---

## Final System Performance

The final Pegasus aircraft achieved:

| Metric | Final Value |
| --- | --- |
| Estimated Takeoff Weight | **18.02 lb** |
| Thrust-to-Weight Ratio | **2.66** |
| Hover Throttle | **~47%** |
| Estimated Mission Endurance | **10.6 min** |
| Maximum Structural Deflection | **0.207 mm** |
| Minimum Factor of Safety | **2.04** |
| First Structural Natural Frequency | **89.985 Hz** |

These results demonstrated that the aircraft met the major structural and propulsion requirements established during the design process.

---

## Testing and Validation

The aircraft was validated through several stages of testing, including:

- Structural finite element analysis
- Modal analysis
- Propulsion sizing
- Sensitivity analysis
- Ground testing
- Hover testing
- Manual flight-control testing
- Autonomous mission testing

The final platform demonstrated stable hover, autonomous mission capability, and successful integration of the structural, propulsion, avionics, and payload subsystems.

<div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; margin: 25px 0;">
  <iframe
    src="https://drive.google.com/file/d/19QqljwDVE41zUtzhSsuOhmK2Lg-SmHYO/preview"
    style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; border: 0;"
    allow="autoplay"
    allowfullscreen>
  </iframe>
</div>

---

## Project Report

Pegasus was completed as a Mechanical Engineering Senior Design project at Santa Clara University for the **Vertical Flight Society Design-Build-Vertical Flight competition**.

The full project included structures, propulsion, avionics, flight controls, autonomous navigation, payload integration, manufacturing, and flight testing.
