---
tags:
  - versions
---

# Hardware Versions

This page tracks the version history of all hardware components. When you work on a robot, identify which version of each component is installed so you can follow the correct documentation.

!!! tip "How to identify your version"
    PCB version is usually silk-screened on the board (e.g., `SPINAL_H7_V2`).
    Firmware version can be read from the rosserial connection or from the CubeIDE project.

---

## Spinal (Flight Controller)

| PCB Version | MCU | Key Changes | Status | Doc |
|-------------|-----|-------------|--------|-----|
| **H7 v2** | STM32H743 | Current production version. Ethernet + FDCAN. | :material-check-circle:{ style="color:green" } **Active** | [Firmware Build](../hardware/common/flight_controller/firmware_build.md) |
| H7 v1 | STM32H743 | Initial H7 design. No FDCAN. | :material-alert:{ style="color:orange" } Legacy | [Firmware Build](../hardware/common/flight_controller/firmware_build.md) |
| F7 | STM32F745 | Original Spinal design. Slower CPU. | :material-close-circle:{ style="color:red" } Deprecated | — |

### Spinal Firmware Branches

| Branch | Compatible PCB | Notes |
|--------|----------------|-------|
| `deploy` | H7 v2 | Main deployment branch |
| `deploy-h7v1` | H7 v1 | Maintenance only |

---

## Neuron (Joint Controller)

| PCB Version | MCU | Key Changes | Status | Robots |
|-------------|-----|-------------|--------|--------|
| **G4 v1** | STM32G431 | Current. FDCAN. Compact form factor. | :material-check-circle:{ style="color:green" } **Active** | Dragon, Spidar |
| F4 v2 | STM32F405 | Previous generation. CAN 2.0B. | :material-alert:{ style="color:orange" } Legacy | Older Dragon |
| F1 | STM32F103 | First generation. Limited CAN. | :material-close-circle:{ style="color:red" } Deprecated | — |

---

## ESC

| PCB / Model | Firmware | Protocol | Status | Robots |
|-------------|----------|----------|--------|--------|
| **Custom ESC v2** | AM32 v2.x | DSHOT300 | :material-check-circle:{ style="color:green" } **Active** | Dragon, Spidar, Beetle |
| Custom ESC v1 | AM32 v1.x | PWM / DSHOT150 | :material-alert:{ style="color:orange" } Legacy | Older platforms |
| BLHeli_32 (off-shelf) | BLHeli_32 | DSHOT300 | :material-check-circle:{ style="color:green" } Compatible | Any |

### AM32 Firmware Versions

| AM32 Version | Release Date | Key Changes |
|-------------|-------------|-------------|
| v2.18 | — | Current stable |
| v2.10 | — | Added telemetry support |
| v1.x | — | Legacy, no bidirectional DSHOT |

---

## Servo (Joint Actuator)

| Model | Protocol | ID Range | Status | Robots |
|-------|----------|----------|--------|--------|
| Dynamixel XM540-W270 | TTL / RS485 | 1–252 | :material-check-circle:{ style="color:green" } Active | Dragon, Spidar |
| Dynamixel MX-64 | TTL | 1–252 | :material-alert:{ style="color:orange" } Legacy | — |

---

## Upper Computer (Onboard PC)

| Model | OS | ROS | Status | Robots |
|-------|----|-----|--------|--------|
| **Khadas VIM4** | Ubuntu 22.04 | ROS Noetic / ROS 2 | :material-check-circle:{ style="color:green" } Active | — |
| Nvidia Jetson Xavier NX | Ubuntu 20.04 | ROS Noetic | :material-check-circle:{ style="color:green" } Active | — |
| Intel Upboard | Ubuntu 18.04 | ROS Melodic | :material-alert:{ style="color:orange" } Legacy | — |

---

## How to Update This Page

When a new hardware version is introduced:

1. Add a row to the relevant table above
2. Update the [Compatibility Matrix](compatibility_matrix.md)
3. Add an entry to [Firmware Changelog](firmware_changelog.md) (for firmware changes)
4. Update the relevant hardware setup doc to note version-specific differences
