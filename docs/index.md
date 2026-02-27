---
hide:
  - navigation
  - toc
---

# Aerial Robot Manual

**A comprehensive reference for development, calibration, and deployment of the Articulated Aerial Robot (AAR) platform.**

This manual covers everything from low-level STM32 firmware development to real-machine deployment, organized so that common knowledge and robot-specific quirks are easy to find separately.

---

## Quick Navigation

<div class="grid cards" markdown>

-   :material-robot:{ .lg .middle } **Robots**

    ---

    Platform-specific guides: from simulation to real-machine deployment.

    [:octicons-arrow-right-24: Dragon](robots/dragon/README.md) ·
    [:octicons-arrow-right-24: Spidar](robots/spidar/README.md) ·
    [:octicons-arrow-right-24: Beetle](robots/beetle/README.md)

-   :material-chip:{ .lg .middle } **Hardware**

    ---

    Spinal, Neuron, ESC, servo, sensors, upper computer setup.

    [:octicons-arrow-right-24: Hardware Overview](overview/hardware_architecture.md)

-   :material-wrench:{ .lg .middle } **Calibration**

    ---

    Motor test, ESC calibration, servo zero-point, IMU calibration.

    [:octicons-arrow-right-24: Calibration Procedures](calibration/motor_test.md)

-   :material-code-braces:{ .lg .middle } **Firmware Development**

    ---

    STM32 build environment, debug with VS Code + OpenOCD.

    [:octicons-arrow-right-24: Dev Environment](firmware_development/dev_environment.md)

-   :material-monitor:{ .lg .middle } **Simulation**

    ---

    Gazebo and MuJoCo simulation setup and usage.

    [:octicons-arrow-right-24: Gazebo Quickstart](simulation/gazebo_quickstart.md)

-   :material-tag-multiple:{ .lg .middle } **Version History**

    ---

    Hardware PCB versions, firmware versions, compatibility matrix.

    [:octicons-arrow-right-24: Hardware Versions](versions/hardware_versions.md)

</div>

---

## Software Stack Overview

```
┌─────────────────────────────────────────────────────┐
│             aerial_robot_control                    │
│   LQI / Fully-Actuated / Custom Controllers         │
└──────────────────┬──────────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────────┐
│              aerial_robot_model                     │
│        Kinematics · Dynamics · Wrench Alloc         │
└──────────────────┬──────────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────────┐
│           aerial_robot_estimation                   │
│     KF Fusion: IMU · GPS · MoCap · VO · Alt        │
└──────────────────┬──────────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────────┐
│             aerial_robot_nerve                      │
│  Spinal (STM32H7): flight controller firmware       │
│  Neuron (STM32F/G): joint servo controller firmware │
└──────────────────┬──────────────────────────────────┘
                   │  (simulated via)
┌──────────────────▼──────────────────────────────────┐
│          aerial_robot_simulation                    │
│             Gazebo  ·  MuJoCo                       │
└─────────────────────────────────────────────────────┘
```

---

## Documentation Status

| Section | Status | Owner |
|---------|--------|-------|
| Firmware Dev Environment | :material-check-circle:{ style="color: green" } Done | — |
| Spinal IMU Calibration | :material-check-circle:{ style="color: green" } Done | — |
| Hardware Versions | :material-progress-clock:{ style="color: orange" } Partial | — |
| Dragon Robot | :material-circle-outline: Stub | — |
| Spidar Robot | :material-circle-outline: Stub | — |
| Beetle Robot | :material-circle-outline: Stub | — |
| Motor Test | :material-circle-outline: Stub | — |
| VIM4 Setup | :material-circle-outline: Stub | — |
| Gazebo Simulation | :material-circle-outline: Stub | — |
| ROS 2 | :material-clock-outline:{ style="color: gray" } Planned | — |

---

!!! tip "How to contribute"
    See the [Contributing Guide](CONTRIBUTING.md) for writing conventions, image guidelines, and how to add a new robot or controller to this manual.
