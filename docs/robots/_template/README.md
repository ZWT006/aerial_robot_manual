---
tags:
  - template
---

# [Robot Name] — Overview

<!-- INSTRUCTIONS: Replace all [placeholders] and remove this comment block.
     Copy this template: cp -r docs/robots/_template docs/robots/<your_robot>
     Then add nav entries in mkdocs.yml under the Robots section. -->

**Applies to:** [Robot Name] platform
**Hardware config:** See [BOM](../../hardware/robot_specific/[name]/bom.md) and [Assembly Notes](../../hardware/robot_specific/[name]/assembly_notes.md)

---

## Platform Overview

<!-- Describe what this robot is and what makes it special.
     - Mechanical design (number of links, DOF, actuator type)
     - Key research contribution / use case
     - Publication reference if any -->

[Robot Name] is a [describe: e.g., "6-link transformable multilink aerial robot with dual-rotor gimbal units"].

Key features:
- [Feature 1]
- [Feature 2]
- [Feature 3]

---

## Hardware Configuration

| Component | Model / Version | Notes |
|-----------|----------------|-------|
| Flight Controller | Spinal H7 v2 | [Overview](../../hardware/common/flight_controller/overview.md) |
| Joint Controller | Neuron G4 v1 | [x] units total |
| ESC | Custom v2 + AM32 v2.18 | [x] units |
| Servo | Dynamixel XM540-W270 | [x] units |
| Upper Computer | VIM4 | [Setup](../../hardware/common/upper_computer/vim4_setup.md) |
| Sensors | [List sensors] | |

---

## Available Controllers

See the [Controllers Overview](controllers/README.md) for all available control algorithms for this platform.

---

## Quick Start

=== "Simulation"
    ```bash
    # [Fill in the actual launch command]
    roslaunch [package] [robot]_bringup.launch simulation:=true
    ```
    → Full guide: [Simulation](simulation.md)

=== "Real Machine"
    ```bash
    # [Fill in the actual launch command]
    roslaunch [package] [robot]_bringup.launch real_machine:=true
    ```
    → Full guide: [Real Machine Setup](real_machine.md)

---

## Related Resources

- Aerial robot repository: `robots/[name]/` package
- URDF: `robots/[name]/urdf/`
- Launch files: `robots/[name]/launch/`
