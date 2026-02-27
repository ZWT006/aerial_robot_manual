---
tags:
  - calibration
  - spinal
---

# Spinal IMU Calibration

Calibrate the IMU (gyroscope, accelerometer, magnetometer) on the Spinal flight controller using the ROS `rqt` GUI.

**Applies to:** All platforms
**Hardware:** Spinal H7 v1 / v2
**Prerequisites:**
- Spinal firmware flashed and running → [Firmware Flash](firmware_flash.md)
- ROS workspace sourced
- Robot placed on a **flat, level surface**

---

## Setup

Connect Spinal to the PC via USB and set serial port permissions:

```bash
chmod 666 /dev/ttyUSB0
```

Launch the Spinal bridge:

```bash
source ~/ros/jsk_aerial_robot_ws/devel/setup.bash
roslaunch spinal bridge.launch
```

Open `rqt` in a separate terminal:

```bash
rqt
```

In rqt: **Plugins** → **Aerial Robot** → **IMU Calibration**

---

## Calibration Procedure

### 1. Gyroscope Calibration

1. Place Spinal on a flat, stable surface — **do not move it during calibration**
2. In rqt IMU Calibration panel, click **Start Calib** under *Gyroscope*
3. Keep the board completely still for **5 seconds**
4. Click **Stop Calib**

### 2. Accelerometer Calibration

1. Keep Spinal level on a flat surface
2. Click **Start Calib** under *Accelerometer*
3. Hold still for 5 seconds
4. Click **Stop Calib**

### 3. Magnetometer Calibration

1. Click **Start Calib** for the **right LSM** (magnetometer)
2. Slowly rotate the Spinal board through **all orientations** — roll 360°, pitch 360°, yaw 360°
3. Watch the point cloud on the right side of the panel form a **complete sphere**
4. Once the sphere is well-formed, click **Stop Calib**

### 4. Magnetic Declination

Set the local magnetic declination value:

| Location | Declination |
|----------|------------|
| Tokyo | `-0.12666` rad |
| [Your city] | [Look up at [ngdc.noaa.gov](https://www.ngdc.noaa.gov/geomag/calculators/magcalc.shtml)] |

Enter the value in the **Magnetic Declination** field in rqt.

---

## Verification

Kill all ROS processes, then press the **reset button** on the Spinal board.

Relaunch bridge and check IMU output:

```bash
rostopic echo /imu
```

**Expected values (robot level):**

| Field | Expected |
|-------|----------|
| `acc_data[0]` (X) | ≈ 0 |
| `acc_data[1]` (Y) | ≈ 0 |
| `acc_data[2]` (Z) | ≈ 9.8 |
| Quaternion → roll | ≈ 0° |
| Quaternion → pitch | ≈ 0° |

!!! tip "Reference"
    See also: [jsk_aerial_robot PR #357](https://github.com/jsk-ros-pkg/jsk_aerial_robot/pull/357)

---

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| `/dev/ttyUSB0` permission denied | USB permissions | `chmod 666 /dev/ttyUSB0` or add udev rule |
| IMU topic not publishing | Bridge not launched | Check `roslaunch spinal bridge.launch` output |
| Accelerometer Z shows 0 instead of 9.8 | Calibration not saved | Repeat calibration, check rqt for errors |
| Magnetometer sphere incomplete | Insufficient rotation coverage | Redo calibration with more thorough orientation coverage |
