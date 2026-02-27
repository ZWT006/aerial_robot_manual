---
tags:
  - versions
---

# Compatibility Matrix

This matrix shows which hardware and firmware versions have been **validated together** on each robot platform.

!!! warning "Always verify before deployment"
    Using untested combinations may cause unexpected behavior. If you validate a new combination, please update this table.

---

## Dragon

| Robot Config | Spinal PCB | Spinal FW Branch | Neuron PCB | ESC | Servo | Upper PC | Validated |
|---|---|---|---|---|---|---|---|
| Dragon v3 (current) | H7 v2 | `deploy` | G4 v1 | Custom v2 + AM32 v2.18 | XM540-W270 | VIM4 | :material-check: Yes |
| Dragon v2 | H7 v1 | `deploy-h7v1` | F4 v2 | Custom v1 + AM32 v1.x | XM540-W270 | Jetson Xavier NX | :material-check: Yes |

---

## Spidar

| Robot Config | Spinal PCB | Spinal FW Branch | Neuron PCB | ESC | Servo | Upper PC | Validated |
|---|---|---|---|---|---|---|---|
| Spidar v1 (current) | H7 v2 | `deploy` | G4 v1 | Custom v2 + AM32 v2.18 | XM540-W270 | VIM4 | :material-check: Yes |

---

## Beetle

| Robot Config | Spinal PCB | Spinal FW Branch | ESC | Upper PC | Validated |
|---|---|---|---|---|---|
| Beetle v1 (current) | H7 v2 | `deploy` | Custom v2 + AM32 v2.18 | VIM4 | :material-check: Yes |

---

## ROS / OS Compatibility

| ROS Version | Ubuntu | Python | Build Tool | Status |
|-------------|--------|--------|-----------|--------|
| ROS 2 Jazzy | 24.04 | 3.12 | colcon | :material-progress-clock: In development |
| ROS Noetic | 20.04 | 3.8 | catkin | :material-check-circle:{ style="color:green" } **Stable** |
| ROS Melodic | 18.04 | 2.7 | catkin | :material-close-circle:{ style="color:red" } Deprecated |

---

## Sensor Compatibility

| Sensor | ROS Driver | Spinal Integration | Notes |
|--------|-----------|-------------------|-------|
| Intel RealSense D435 | `realsense2_camera` | External | Depth + RGB |
| Intel RealSense T265 | `realsense2_camera` | External | Tracking camera |
| Holybro M8N GPS | `aerial_robot_nerve/spinal` | Built-in | Standard GPS |
| Holybro M8P RTK | `aerial_robot_nerve/spinal` | Built-in | RTK GPS |
| Vicon | `vicon_bridge` | External | Motion capture |
| OptiTrack | `mocap_optitrack` | External | Motion capture |

---

## Adding a Validated Combination

When you test a new hardware combination on a real robot:

1. Add a row to the robot's table above
2. Set "Validated" to `:material-check: Yes`
3. If it breaks something, add a row with `:material-close: No` and a note
4. Commit with message `docs: update compatibility matrix for <robot> <config>`
