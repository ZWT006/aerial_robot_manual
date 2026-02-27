# Aerial Robot Manual

A handbook for developing and deploying Articulated Aerial Robot (AAR) platforms based on the
[`aerial_robot`](https://github.com/ZWT006/aerial_robot/tree/deploy) repository.

Covers hardware configuration, calibration, firmware development, simulation, and per-robot
launch procedures for Dragon, Spidar, and Beetle platforms.

---

## Supported Robots

| Robot   | Description                                      |
|---------|--------------------------------------------------|
| Dragon  | Multi-link transformable aerial robot            |
| Spidar  | Spider-inspired multi-rotor with leg manipulation |
| Beetle  | Compact tilted-rotor platform                    |

---

## Setting Up the Manual Dev Environment

### Prerequisites

- [conda](https://docs.conda.io/en/latest/miniconda.html) (recommended) or Python 3.10+

### Install

```bash
# 1. Clone this repository
git clone <this-repo-url>
cd aerial_robot_manual

# 2. Create and activate a conda environment
conda create -n mkdocs python=3.11 -y
conda activate mkdocs

# 3. Install dependencies
pip install -r requirements.txt
```

### Local Preview

```bash
conda activate mkdocs
mkdocs serve
```

Open [http://127.0.0.1:8000](http://127.0.0.1:8000) in your browser.

### Build Static Site

```bash
mkdocs build          # Output → site/
```

---

## Repository Structure

```
aerial_robot_manual/
├── docs/
│   ├── index.md                     # Homepage
│   ├── CONTRIBUTING.md              # Writing guide (read before editing)
│   ├── assets/
│   │   ├── images/                  # Images, organised by category
│   │   │   ├── hardware/            #   spinal / neuron / esc / servo / sensors
│   │   │   ├── robots/              #   dragon / spidar / beetle
│   │   │   ├── calibration/
│   │   │   ├── simulation/
│   │   │   └── software/
│   │   └── files/                   # Datasheets, 3D models, schematics
│   ├── stylesheets/
│   │   └── extra.css                # Custom CSS (version badges, robot tags)
│   │
│   ├── overview/                    # System & hardware architecture overview
│   ├── workspace_setup/             # ROS workspace build, network setup
│   │
│   ├── hardware/
│   │   ├── common/                  # Shared hardware documentation
│   │   │   ├── flight_controller/   #   Spinal (STM32H7): flash, IMU cal
│   │   │   ├── joint_controller/    #   Neuron (STM32G4/F4/F1): ID assignment
│   │   │   ├── upper_computer/      #   VIM4 / Jetson setup
│   │   │   ├── esc/                 #   ESC wiring, AM32 configuration
│   │   │   ├── servo/               #   Dynamixel ID assignment, zero calibration
│   │   │   ├── sensors/             #   IMU, GPS-RTK, RealSense, MoCap, rangefinder
│   │   │   └── 3d_printing/         #   Material guide, print settings
│   │   └── robot_specific/          # Per-robot assembly notes
│   │       ├── dragon/
│   │       ├── spidar/
│   │       └── beetle/
│   │
│   ├── firmware_development/        # Spinal & Neuron firmware dev guides
│   │   ├── dev_environment.md       #   STM32CubeIDE + OpenOCD + VS Code setup
│   │   ├── debug_guide.md           #   Cortex-Debug / OpenOCD debug workflow
│   │   ├── spinal_architecture.md
│   │   ├── neuron_architecture.md
│   │   ├── rosserial_protocol.md
│   │   └── add_new_message.md
│   │
│   ├── calibration/                 # Motor test, ESC, servo, IMU, thrust
│   │
│   ├── software/
│   │   ├── control/                 # Controller types, gain tuning, trajectory
│   │   ├── estimation/              # EKF, sensor fusion
│   │   ├── navigation/              # Flight states, keyboard / joystick control
│   │   └── visualization/           # RViz, rqt plugins, rosbag workflow
│   │
│   ├── simulation/                  # Gazebo / MuJoCo quickstart
│   │
│   ├── robots/
│   │   ├── _template/               # Template — copy when adding a new robot
│   │   ├── dragon/
│   │   │   └── controllers/         #   Per-controller docs (LQI, …)
│   │   ├── spidar/
│   │   │   └── controllers/
│   │   └── beetle/
│   │       └── controllers/
│   │
│   ├── safety/                      # Pre-flight checklist, emergency procedures
│   ├── versions/                    # Hardware & firmware version history (linked inline)
│   ├── ros2/                        # ROS 2 placeholder (in development)
│   └── reference/                   # ROS API, launch params, glossary, tags
│
├── mkdocs.yml                       # MkDocs + Material configuration
├── requirements.txt                 # Python dependencies
└── .gitignore
```

---

## Contributing

See [docs/CONTRIBUTING.md](docs/CONTRIBUTING.md) for the full writing guide, including:

- Common vs. robot-specific content rules
- Version tracking conventions (inline admonitions, not a separate section)
- Image format and compression guidelines
- How to add a new robot or controller variant
