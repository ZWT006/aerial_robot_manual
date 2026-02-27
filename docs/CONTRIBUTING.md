# Contributing Guide

This guide covers how to write, update, and maintain documentation in this manual.

---

## File Organization Principles

### Common vs. Robot-Specific

```
hardware/common/        ← Hardware behavior that is the same regardless of which robot it's on
hardware/robot_specific/← Assembly quirks or BOM differences specific to one robot
robots/<name>/          ← User-facing guide for operating that specific robot
                          (links to common/ for shared procedures)
```

**Rule of thumb:** If the same step applies to Dragon, Spidar, and Beetle identically, it goes in `common/`. If it's only true for one robot, it goes in `robot_specific/` or `robots/<name>/`.

### Controller Variants

Each robot can have multiple controllers developed by different team members. Place controller-specific docs under:

```
robots/<name>/controllers/<controller_name>/
├── overview.md     ← What it does, who maintains it, which branch/repo
├── simulation.md   ← How to run in sim
└── tuning.md       ← Parameters and tuning guide
```

The `robots/<name>/controllers/README.md` acts as the index listing all available controllers and their status.

---

## Page Template

Every new page should follow this structure:

```markdown
---
tags:
  - dragon          # Which robots this applies to (dragon/spidar/beetle/all)
  - spinal-h7v2     # Which hardware version (if applicable)
---

# Page Title

**Applies to:** Dragon · Spidar  ← or "All platforms"
**Hardware version:** Spinal H7 v2+  ← omit if not hardware-specific
**Prerequisites:** [Link to prerequisite page]

## Overview

Brief description of what this page covers.

## Steps / Content

...

## Verification

How to confirm the procedure succeeded.

## Common Issues

| Symptom | Cause | Fix |
|---------|-------|-----|

## Related

- [Related page 1](../path/to/page.md)
```

---

## Version Tracking

When a procedure changes because of a **hardware revision** or **firmware update**:

1. Update the relevant page with the new information
2. Use admonitions to clearly flag version-specific differences:

```markdown
!!! note "Spinal H7 v1 only"
    On v1 boards, use `openocd_h743_stlink_v2.cfg`.
    On v2 boards, see [firmware_flash.md](firmware_flash.md).
```

3. Update the [Hardware Versions](versions/hardware_versions.md) table
4. Update the [Compatibility Matrix](versions/compatibility_matrix.md) if needed
5. Add an entry to [Firmware Changelog](versions/firmware_changelog.md)

### Version Admonition Conventions

| Type | Use case | MkDocs admonition type |
|------|----------|----------------------|
| Hardware version note | PCB rev-specific step | `note "Spinal H7 v2 only"` |
| Deprecated procedure | Old method still works but not recommended | `warning "Deprecated"` |
| Breaking change | Old method no longer works | `danger "Breaking change in v2.x"` |
| Future / ROS2 | Placeholder for upcoming content | `info "ROS 2 support coming"` |

---

## Image Guidelines

### Directory Structure

```
docs/assets/images/
├── hardware/
│   ├── spinal/          ← Spinal PCB photos, wiring diagrams
│   ├── neuron/
│   ├── esc/
│   ├── servo/
│   └── sensors/
├── robots/
│   ├── dragon/          ← Dragon photos, transformation poses
│   ├── spidar/
│   └── beetle/
├── calibration/         ← Screenshots of rqt, calibration setups
├── simulation/          ← Gazebo/MuJoCo screenshots
└── software/            ← RViz configs, rqt screenshots
```

### Image Format Rules

| Use case | Format | Max size |
|----------|--------|----------|
| Photo (robot, hardware) | `.webp` (convert from JPG/PNG) | 800 KB |
| Diagram / schematic | `.png` with transparent bg | 500 KB |
| Screenshot | `.webp` | 400 KB |
| Vector diagram | `.svg` | — |

**Compress before committing:**
```bash
# Convert and compress to webp
cwebp -q 80 input.jpg -o docs/assets/images/hardware/spinal/pcb_v2.webp

# Or use ImageMagick
convert input.png -quality 85 docs/assets/images/robots/dragon/overview.webp
```

### Referencing Images

```markdown
![Spinal H7 v2 PCB](../../assets/images/hardware/spinal/pcb_v2.webp)
```

Images automatically get the lightbox (click to zoom) from the `glightbox` plugin.

### Large Files (3D Models, PDFs)

Place in `docs/assets/files/`:
```
docs/assets/files/
├── datasheets/          ← Component datasheets (PDF)
├── 3d_models/           ← STL / STEP files for 3D printing
└── schematics/          ← PCB schematics (PDF)
```

---

## Adding a New Robot

1. Copy the template directory:
   ```bash
   cp -r docs/robots/_template docs/robots/<new_robot_name>
   ```
2. Fill in each page following the template instructions
3. Add hardware-specific assembly notes under `docs/hardware/robot_specific/<new_robot_name>/`
4. Add BOM under `docs/hardware/robot_specific/<new_robot_name>/bom.md`
5. Add the robot to `docs/overview/supported_robots.md`
6. Add nav entries in `mkdocs.yml` under the `Robots:` section

---

## Adding a New Controller

1. Create directory: `docs/robots/<name>/controllers/<controller_name>/`
2. Create at minimum `overview.md` with:
   - What the controller does
   - Which branch / external repo contains the code
   - Who maintains it
   - Current status (experimental / stable / deprecated)
3. Update `docs/robots/<name>/controllers/README.md` with a row in the controllers table
4. Add nav entries in `mkdocs.yml`

---

## Local Preview

```bash
# Install dependencies
pip install -r requirements.txt

# Start dev server with live reload
mkdocs serve

# Open http://127.0.0.1:8000
```

## Building & Deploying

```bash
# Build static site
mkdocs build

# Deploy to GitHub Pages (one-time)
mkdocs gh-deploy

# Versioned deploy with mike
mike deploy --push --update-aliases 1.0 latest
mike set-default --push latest
```
