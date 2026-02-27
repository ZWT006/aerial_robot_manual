---
tags:
  - hardware
  - spinal
---

# Spinal Flight Controller

## Overview

Spinal is the flight controller used in the JSK aerial robot systems. This page covers the initial setup procedures: IMU calibration, udev configuration, and motor operation verification.

---

## Initial Setup Procedure

### Prerequisites

- Spinal firmware flashed and running → [Firmware Flash](firmware_flash.md)
- ROS workspace set up and sourced
- Drone placed on a **flat, level surface** during all procedures

---

!!! info "When is this needed?"
    Just for Spinal standalone operation. You can do these configurations with your host pc and spinal.

## 1. IMU Calibration

Perform the following with Spinal connected to the PC via USB cable.

### Launch

```bash
source ~/ros/jsk_aerial_robot_ws/devel/setup.bash
roslaunch spinal bridge.launch
```

Open `rqt` in a separate terminal:

```bash
rqt
```

In rqt: **Plugins → Aerial Robot → IMU Calibration**

### 1-1. Gyroscope Calibration

1. Place Spinal on a flat, stable surface — do not move it during calibration
2. Click **Start Calib** under *Gyroscope* in rqt
3. Keep the board completely still for **5 seconds**
4. Click **Stop Calib**

### 1-2. Accelerometer Calibration

1. Keep Spinal level on a flat surface
2. Click **Start Calib** under *Accelerometer* in rqt
3. Hold still for **5 seconds**
4. Click **Stop Calib**

### 1-3. Magnetometer Calibration

1. Click **Start Calib** for the **right LSM** (magnetometer) in rqt
2. Slowly rotate the Spinal board through **all orientations** — roll, pitch, and yaw in every direction
3. Watch the point cloud on the right side of the panel gradually form a **complete sphere**
4. Once the sphere is well-formed, click **Stop Calib**, then click **Save**

### 1-4. Magnetic Declination

Set the local magnetic declination value in the *Magnetic Declination* field in rqt:

| Location | Declination |
|----------|-------------|
| Tokyo | `-0.12666` rad |
| Other cities | Look up at [ngdc.noaa.gov](https://www.ngdc.noaa.gov/geomag/calculators/magcalc.shtml) |

### Post-Calibration Verification

1. Kill all ROS processes
2. Press the **reset button** on Spinal
3. Relaunch the bridge and check IMU output:

```bash
rostopic echo /imu
```

**Expected values (drone level):**

| Field | Expected |
|-------|----------|
| `acc_data[0]` (X-axis) | ≈ 0 |
| `acc_data[1]` (Y-axis) | ≈ 0 |
| Quaternion → roll | ≈ 0° |
| Quaternion → pitch | ≈ 0° |

!!! note "Reference"
    See also: [jsk_aerial_robot PR #357](https://github.com/jsk-ros-pkg/jsk_aerial_robot/pull/357)

### Troubleshooting

| Symptom | Fix |
|---------|-----|
| `acc_data` becomes `nan` | Redo accelerometer calibration → Save → kill all processes → unplug and replug USB → retry |

---

## 2. udev Setup

Configuring a udev rule serves two purposes:

1. **Stable device name** — Spinal always appears as `/dev/flight_controller` regardless of plug order
2. **Latency configuration** — Sets `latency_timer=1` on the FTDI chip, which is **critical** for stable rosserial message delivery from Spinal

Reference: [jsk_aerial_robot wiki: udev](https://github.com/jsk-ros-pkg/jsk_aerial_robot/wiki/udev)

### Step 1 — Find Device Identifiers

With Spinal connected, run:

```bash
udevadm info -q property -n /dev/ttyUSB0 | grep -E "ID_SERIAL_SHORT=|ID_VENDOR_ID=|ID_MODEL_ID="
```

Example output:

```
ID_MODEL_ID=6001
ID_SERIAL_SHORT=AL053JRP
ID_VENDOR_ID=0403
```

Or use the verbose form to get all attributes:

```bash
sudo udevadm info -a /dev/ttyUSB0
```

Note down `idVendor`, `idProduct`, and `serial` — you will fill these into the rule file below.

Reference: [https://smdn.jp/electronics/udev_create_persistent_usb_device_symlink/](https://smdn.jp/electronics/udev_create_persistent_usb_device_symlink/)

### Step 2 — Create the Rules File

Create `/etc/udev/rules.d/99-aerial-robot-dev.rules` (the `99` prefix means highest loading priority):

```
KERNEL=="ttyUSB*", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{serial}=="A907HR6F", SYMLINK+="flight_controller"
ACTION=="add", SUBSYSTEM=="usb-serial", DRIVER=="ftdi_sio", ATTR{latency_timer}="1"
```

- **Line 1**: replace `idVendor`, `idProduct`, and `serial` with the values from Step 1.
- **Line 2**: sets `latency_timer` to 1 ms. Without this, rosserial messages from Spinal arrive unstably.

### Step 3 — Reload and Test

```bash
sudo udevadm control --reload-rules && sudo udevadm trigger
```

Unplug and replug Spinal. Verify:

```bash
ls -la /dev/flight_controller          # symlink should exist
cat /sys/bus/usb-serial/devices/ttyUSB0/latency_timer  # should print 1
```

If you don't have permission to access `/dev/ttyUSB*`:

```bash
sudo adduser $USER dialout
# then log out and back in
```

---

### Troubleshooting: `latency_timer` Not Taking Effect

**Symptom:** After applying the rules above, `latency_timer` still reads a value other than `1`, and launching Spinal produces warnings like:

```
IMU: bad timestamp. curr time stamp: %f, prev time stamp: %f
```

**Cause:** The `ATTR{latency_timer}="1"` syntax writes to sysfs at device match time, but a race condition between udev and the kernel driver initialization can prevent the write from sticking.

**Fix:** Add an explicit `RUN+=` action that writes to sysfs after a short delay, in addition to the symlink rule. Replace the contents of `99-aerial-robot-dev.rules` with:

```
# 1) Create symlink for USB device
ACTION=="add", SUBSYSTEM=="tty", KERNEL=="ttyUSB[0-9]*", \
  ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{serial}=="AL053JRP", \
  SYMLINK+="flight_controller", GROUP="dialout", MODE="0660"

# 2) Set latency_timer via sysfs (robust)
ACTION=="add", SUBSYSTEM=="tty", KERNEL=="ttyUSB[0-9]*", \
  ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{serial}=="AL053JRP", \
  RUN+="/bin/sh -c 'sleep 0.2; echo 1 > /sys/bus/usb-serial/devices/%k/latency_timer'"
```

Key differences from the basic template:

| | Basic template | Robust version |
|---|---|---|
| Symlink rule | `KERNEL=="ttyUSB*"` | `KERNEL=="ttyUSB[0-9]*"` with explicit `ACTION=="add"` |
| Latency write | `ATTR{latency_timer}="1"` (in-place) | `RUN+=` shell command with 0.2 s delay |
| Why it works | Writes at match time — may race | Deferred write after driver is fully initialized |

After editing the file, reload and replug:

```bash
sudo udevadm control --reload-rules && sudo udevadm trigger
# unplug and replug Spinal
cat /sys/bus/usb-serial/devices/ttyUSB0/latency_timer  # must print 1
```

---

!!! info "When is this needed?"
    Not required for Spinal standalone operation. Must be done before using `bringup.launch` with a full robot.

## 3. Servo Configuration

### Prerequisites

- Spinal connected to the PC via Micro-USB cable
- Dynamixel servo(s) powered (voltage applied)

### Launch

```bash
source ~/ros/jsk_aerial_robot_ws/devel/setup.bash
roslaunch spinal bridge.launch
```

Open `rqt` in a separate terminal:

```bash
rqt
```

### Initial Configuration

In rqt: **Plugins → Aerial Robot → Board Configurator**

Configure each servo according to your hardware setup. Make sure to set the **External Encoder Flag** correctly to match the actual hardware configuration.

### Verify Servo Readout

In rqt: **Plugins → Aerial Robot → Servo Monitor**

Check that Dynamixel servo values are being read correctly.

!!! tip
    If values are not appearing, press the reset button on Spinal or Neuron once, then relaunch rqt.

### Joint Angle Offset Calibration

To apply an angle offset to a joint:

1. Turn the servo **Off** in the Servo Monitor panel
2. Enter the desired value in the **Homing Offset** field
3. This performs joint calibration and applies the angle offset

### Sending Rotation Commands from Terminal

To send a rotation command directly via topic:

```bash
rostopic pub -1 /servo/target_states spinal/ServoControlCmd "index: [<index>]
angles: [<angle>]"
```

!!! note
    The value for `index` corresponds to the number shown in the **index column** of the Servo Monitor.

---

## 4. Motor Operation Test (PWM Test)

### Wiring

| Configuration | ESC connects to |
|---------------|----------------|
| Spinal standalone | Spinal PWM port |
| Spinal + Neuron | Neuron PWM port |

1. Power on the ESC
2. Connect Spinal to the PC via USB cable (Micro-B)
3. On boot, each board sends **PWM 0.5** — a beep from the motors confirms readiness

### Sending PWM Commands

```bash
source ~/ros/jsk_aerial_robot_ws/devel/setup.bash
roslaunch spinal bridge.launch
```

In a separate terminal, publish a PWM command:

```bash
rostopic pub -1 /pwm_test spinal/PwmTest "motor_index: [0]
pwms: [0.65]"
```

| PWM Value | Motor Behavior |
|-----------|---------------|
| `0.5` | Stop |
| Greater than `0.5` (e.g. `0.65`) | Spin |

---


## Related

- [Firmware Flash](firmware_flash.md)
- [Firmware Build](firmware_build.md)
- [IMU Calibration](imu_calibration.md)
