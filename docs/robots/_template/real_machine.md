---
tags:
  - template
---

# [Robot Name] — Real Machine Setup & Launch

**Prerequisites:**

- [ ] Simulation runs correctly → [Simulation Guide](simulation.md)
- [ ] All hardware calibrated → [Motor Test](../../../calibration/motor_test.md), [Servo Calibration](../../../calibration/servo_calibration.md), [IMU Calibration](../../../hardware/common/flight_controller/imu_calibration.md)
- [ ] Spinal firmware flashed → [Firmware Flash](../../../hardware/common/flight_controller/firmware_flash.md)
- [ ] VIM4 / Jetson configured → [VIM4 Setup](../../../hardware/common/upper_computer/vim4_setup.md)
- [ ] Network configured → [Network Setup](../../../workspace_setup/network_setup.md)

---

## Pre-flight Checklist

!!! danger "Do not skip this checklist before every flight"
    See also the [Universal Pre-flight Checklist](../../../safety/pre_flight_checklist.md).

**Mechanical:**
- [ ] All propellers securely fastened, no cracks
- [ ] All servo connections secure, cables routed properly
- [ ] Frame screws tightened
- [ ] Battery fully charged and secured

**Electronics:**
- [ ] ST-Link disconnected (debug cable should not be connected during flight)
- [ ] All motor spin directions correct (verified in [Motor Test](../../../calibration/motor_test.md))
- [ ] ESC armed LED pattern normal

**Software:**
- [ ] `roscore` running on VIM4
- [ ] Spinal connected and `/imu` topic publishing
- [ ] Localization source active (MoCap / GPS RTK)

---

## Power-On Sequence

!!! warning "Order matters"
    Incorrect power-on order can cause ESC desync or communication errors.

1. Place robot on flat surface
2. Connect battery **while holding robot level**
3. Wait for Spinal IMU initialization (LED pattern: [describe])
4. SSH into VIM4: `ssh robot@<ip>`
5. Launch roscore and bringup (see below)

---

## Launch Commands

```bash
# On VIM4 (via SSH):
source ~/ros/jsk_aerial_robot_ws/devel/setup.bash

# [Fill in actual launch command]
roslaunch [package] [robot]_bringup.launch real_machine:=true
```

**Key launch parameters:**

| Parameter | Default | Description |
|-----------|---------|-------------|
| `real_machine` | `false` | Must be `true` for real hardware |
| `[param]` | `[value]` | [Description] |

---

## Arming and Flight

```bash
# Keyboard control (in separate terminal):
roslaunch [package] teleop_keyboard.launch

# Key bindings:
# [Fill in key bindings]
```

---

## Power-Off Sequence

1. Land the robot (disarm first)
2. Kill the ROS launch
3. Disconnect battery **immediately** after motors stop

---

## Robot-Specific Notes

!!! note "Special considerations for [Robot Name]"
    <!-- Fill in anything that is unique to THIS robot and not covered by common docs:
         - Unusual startup behavior
         - Known hardware quirks
         - Things that have caused accidents before -->
    *TODO: Fill in robot-specific notes.*
