---
tags:
  - firmware
  - spinal
  - neuron
---

# Firmware Development Environment

Setup guide for building and flashing Spinal and Neuron MCU firmware on Ubuntu.

**Applies to:** All platforms
**Hardware:** Spinal H7 v1/v2 · Neuron G4/F4/F1
**Host OS:** Ubuntu 22.04 / 24.04

---

## Hardware Requirements

| Item | Description |
|------|-------------|
| MCU | STM32H743VITx (Cortex-M7) for Spinal H7 |
| Debugger | ST-Link V2 |
| Wiring | SWCLK, SWDIO, GND, 5V (**do not connect NRST**) |
| RTOS | FreeRTOS |
| Communication | UART (rosserial) / Ethernet (LwIP) / FDCAN |

!!! warning "NRST not connected"
    The NRST (hardware reset) line is **not used** in this project. Software reset via the Cortex-M AIRCR register is used instead. Do not connect NRST, and do not configure OpenOCD to use it.

---

## Software Requirements

| Software | Tested Version | Purpose |
|----------|---------------|---------|
| Ubuntu | 22.04 / 24.04 LTS | Host OS |
| STM32CubeIDE | 1.18.0 | Build firmware (includes ARM GCC toolchain) |
| OpenOCD | 0.12.0 | Debug server, connects to ST-Link |
| VS Code | Latest | Code editor + debugger frontend |
| Cortex-Debug extension | 1.12.1+ | ARM Cortex-M debug support in VS Code |
| libncurses5 / libtinfo5 | 6.1 | Runtime dependency for the bundled `arm-none-eabi-gdb` |

---

## Step 1 — Install STM32CubeIDE

Download the installer from the [ST website](https://www.st.com/en/development-tools/stm32cubeide.html#get-software).

Recommended versions: **1.6.1** or **1.18.0**

```bash
# Unzip the installer package
unzip st-stm32cubeide_1.18.0_*.sh.zip

# Run the installer (accept all prompts, use default install path)
sudo sh st-stm32cubeide_1.18.0_*.sh
# When long license text appears, press 'q' to skip to the prompts.
```

Default installation path: `~/st/stm32cubeide_1.18.0/`

The ARM GCC toolchain is bundled at:
```
~/st/stm32cubeide_1.18.0/plugins/
  com.st.stm32cube.ide.mcu.externaltools.gnu-tools-for-stm32.13.3.rel1.linux64_1.0.0.*/
  tools/bin/arm-none-eabi-gcc
```

Create your workspace directory:
```bash
mkdir -p ~/STM32Cube    # Default workspace for CubeIDE
```

---

## Step 2 — Install OpenOCD

```bash
sudo apt update
sudo apt install openocd

# Verify
openocd --version
# Expected: Open On-Chip Debugger 0.12.0
```

The Spinal project includes a pre-configured OpenOCD config:
- `boards/stm32H7/STM32CubeIDE/openocd_h743_stlink_v2.cfg`

---

## Step 3 — Fix GDB Library Dependencies (Ubuntu 22.04+)

The `arm-none-eabi-gdb` bundled with CubeIDE requires `libncurses.so.5` and `libtinfo.so.5`.
Ubuntu 22.04+ ships with v6 only, so these must be installed manually.

```bash
mkdir -p ~/drivers/libncurses5
cd ~/drivers/

# Download the Ubuntu 18.04 packages (compatible)
wget http://archive.ubuntu.com/ubuntu/pool/universe/n/ncurses/libncurses5_6.1-1ubuntu1.18.04.1_amd64.deb
wget http://archive.ubuntu.com/ubuntu/pool/universe/n/ncurses/libtinfo5_6.1-1ubuntu1.18.04.1_amd64.deb

# Extract to a local path (no system pollution)
dpkg-deb -x libncurses5_6.1-1ubuntu1.18.04.1_amd64.deb ~/drivers/libncurses5/
dpkg-deb -x libtinfo5_6.1-1ubuntu1.18.04.1_amd64.deb ~/drivers/libncurses5/
```

After extraction:
```
~/drivers/libncurses5/lib/x86_64-linux-gnu/
├── libncurses.so.5 -> libncurses.so.5.9
├── libncurses.so.5.9
├── libtinfo.so.5 -> libtinfo.so.5.9
└── libtinfo.so.5.9
```

!!! info "Automatic path handling"
    The `.vscode/arm-gdb.sh` wrapper in the Spinal project sets `LD_LIBRARY_PATH` automatically. No manual configuration is needed once VS Code is set up.

---

## Step 4 — Install VS Code Extensions

```bash
code --install-extension marus25.cortex-debug    # ARM Cortex-M debug
code --install-extension ms-vscode.cpptools       # C/C++ IntelliSense
```

| Extension | ID | Purpose |
|-----------|----|---------|
| Cortex-Debug | `marus25.cortex-debug` | ARM debug adapter |
| C/C++ | `ms-vscode.cpptools` | IntelliSense, syntax highlighting |

---

## Step 5 — Fix ST-Link USB Permissions

```bash
sudo tee /etc/udev/rules.d/99-stlink.rules << 'EOF'
ATTRS{idVendor}=="0483", ATTRS{idProduct}=="3748", MODE="0666", GROUP="plugdev"
EOF

sudo udevadm control --reload-rules
sudo udevadm trigger
```

Verify the ST-Link is detected:
```bash
lsusb | grep STMicro
# Expected: Bus xxx Device xxx: ID 0483:3748 STMicroelectronics ST-LINK/V2
```

---

## Step 6 — Open and Build the Project

### Import into CubeIDE

1. Open STM32CubeIDE
2. `File` → `Open Projects from File System`
3. Select the project directory:
   - **Spinal H7:** `aerial_robot_nerve/spinal/mcu_project/boards/stm32H7/STM32CubeIDE`
   - **Neuron G4:** `aerial_robot_nerve/neuron/neuron_g4/STM32CubeIDE`
4. `Project` → `Clean Project` then `Build Project` (`Ctrl+B`)

Build artifacts appear in `STM32CubeIDE/Debug/`:
- `spinal.elf` — executable with debug symbols
- `spinal.bin` — raw binary for flashing
- `spinal.map` — memory map

---

## Next Steps

- [Debug with VS Code + OpenOCD](debug_guide.md)
- [Flash firmware to board](../hardware/common/flight_controller/firmware_flash.md)
- [Spinal firmware architecture](spinal_architecture.md)

---

## Project Directory Structure (Reference)

```
boards/stm32H7/
├── .vscode/
│   ├── arm-gdb.sh              # GDB wrapper — sets LD_LIBRARY_PATH for libncurses5
│   ├── c_cpp_properties.json   # IntelliSense include paths
│   └── launch.json             # 3 debug configurations
├── Inc/                        # Header files
├── Src/
│   ├── main.c                  # CubeMX-generated peripheral init
│   └── main.cpp                # FreeRTOS task implementations
├── Drivers/                    # STM32 HAL drivers
├── Middlewares/                 # FreeRTOS + LwIP
├── ros_lib/                    # rosserial library
├── STM32CubeIDE/
│   ├── Debug/
│   │   ├── spinal.elf
│   │   └── spinal.bin
│   └── openocd_h743_stlink_v2.cfg
└── spinal.ioc                  # CubeMX peripheral configuration
```
