---
tags:
  - firmware
  - spinal
  - debug
---

# Firmware Debug Guide — VS Code + OpenOCD

This guide describes how to debug Spinal (STM32H743) firmware using VS Code with the Cortex-Debug extension and OpenOCD.

**Prerequisites:** [Dev Environment](dev_environment.md) fully set up.

---

## TL;DR Quick Start

```bash
# 1. Connect ST-Link
lsusb | grep STMicro   # Should show ST-LINK/V2

# 2. Open project in VS Code
code <aerial_robot_nerve>/spinal/mcu_project/boards/stm32H7/

# 3. Press F5, select "STM32H743 Debug (Launch)"
```

---

## Three Debug Modes

Select from the VS Code debug panel (`Ctrl+Shift+D`):

### Mode 1: `STM32H743 Debug (Launch)` ⭐ Recommended

**Use case:** Firmware is already flashed; you just want to attach a debugger.

**Flow:** VS Code → auto-starts OpenOCD → ST-Link → software reset → loads symbols → stops at `main()`.

```bash
# Just press F5
```

### Mode 2: `STM32H743 Debug (Flash + Launch)`

**Use case:** After modifying and rebuilding firmware, flash the new binary and debug.

**Flow:** VS Code → OpenOCD → reset → flash ELF via GDB `load` command → reset → stop at `main()`.

!!! warning "Build first in CubeIDE"
    Build the project in CubeIDE (`Ctrl+B`) before using this mode. VS Code does not build the firmware.

### Mode 3: `STM32H743 Debug (Attach to OpenOCD)`

**Use case:** Manual OpenOCD control, or troubleshooting connection problems.

```bash
# Terminal 1: Start OpenOCD manually
openocd -f STM32CubeIDE/openocd_h743_stlink_v2.cfg
# Wait for: "Listening on port 3333 for gdb connections"

# Then press F5 in VS Code with Mode 3 selected
```

---

## VS Code Configuration Files

The `.vscode/` directory in the project is pre-configured:

```
.vscode/
├── arm-gdb.sh              # Sets LD_LIBRARY_PATH for libncurses5, then calls arm-none-eabi-gdb
├── c_cpp_properties.json   # IntelliSense: include paths, macro definitions
└── launch.json             # The three debug configurations above
```

!!! note "If your CubeIDE path is different"
    Edit `arm-gdb.sh` to match your actual CubeIDE installation path if it differs from the default `~/st/stm32cubeide_1.18.0/`.

---

## Debug Features

| Feature | Shortcut | Description |
|---------|----------|-------------|
| Breakpoints | Click line margin | Halt execution at a specific line |
| Step Over | F10 | Execute current line, don't enter functions |
| Step Into | F11 | Enter the called function |
| Step Out | Shift+F11 | Run until current function returns |
| Continue | F5 | Run until next breakpoint |
| Restart | — | Software reset and restart from `main()` |
| Variables | VARIABLES panel | View local and global variable values |
| Call Stack | CALL STACK panel | See the function call chain |
| Peripheral Registers | XPERIPHERALS panel | View STM32 peripheral register values (requires SVD file) |

### Useful GDB Console Commands

In the Debug Console (`Ctrl+\``):

```gdb
# View all CPU registers
info registers

# Read memory at address
x/16xw 0x24040000

# Print a variable value
print imu_data_

# List FreeRTOS tasks and their states
info threads

# Manually trigger a software reset
monitor reset init
```

---

## Build in CubeIDE (Reference)

When you need to rebuild firmware:

1. Open CubeIDE with the project imported
2. `Project` → `Build Project` (or `Ctrl+B`)
3. Watch the console for errors
4. Build output: `STM32CubeIDE/Debug/spinal.elf` and `spinal.bin`

To flash directly from CubeIDE (without VS Code):

1. `Run` → `Debug As` → `STM32 C/C++ Application`
2. CubeIDE flashes and starts a debug session

---

## Troubleshooting

### GDB fails: `libncurses.so.5: cannot open shared object file`

**Cause:** libncurses5 not installed or `arm-gdb.sh` path is wrong.

**Fix:** Follow [Dev Environment Step 3](dev_environment.md#step-3-fix-gdb-library-dependencies-ubuntu-2204).

---

### OpenOCD fails: `Error: libusb_open() failed` or `Error: open failed`

**Cause:** Missing udev rules for ST-Link.

**Fix:**
```bash
sudo tee /etc/udev/rules.d/99-stlink.rules << 'EOF'
ATTRS{idVendor}=="0483", ATTRS{idProduct}=="3748", MODE="0666", GROUP="plugdev"
EOF
sudo udevadm control --reload-rules && sudo udevadm trigger
```

---

### Flash fails: `timeout waiting for algorithm`

**Cause:** STM32H7 dual-bank Flash architecture + no NRST → OpenOCD `flash write_image` times out.

**Fix:** Use **Mode 1 (Launch)** for debugging without flashing. For flashing, use **Mode 2 (Flash + Launch)** which uses GDB `load` (more reliable), or flash separately with STM32CubeProgrammer:

```bash
st-flash write spinal.bin 0x08000000
```

---

### Debugger stops inside FreeRTOS internals instead of `main()`

**Cause:** Using Attach mode on a running FreeRTOS system — it's already past `main()`.

**Fix:** In the Debug Console:
```gdb
monitor reset init
continue
```
Or switch to **Mode 1 (Launch)** which resets automatically.

---

### VS Code shows `"main.c" not found in compile_commands.json`

**Cause:** CubeIDE's generated `compile_commands.json` is incomplete.

**Fix:** This is harmless. The `c_cpp_properties.json` already defines all include paths and macros directly. IntelliSense works correctly despite this warning.
