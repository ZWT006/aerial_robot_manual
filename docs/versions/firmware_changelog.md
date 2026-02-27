---
tags:
  - versions
---

# Firmware Changelog

Track significant changes to embedded firmware (Spinal, Neuron, ESC). This is distinct from the code repository's git log — it focuses on changes that **affect hardware operation, calibration, or wiring**.

!!! note "Format"
    Add entries in **reverse chronological order** (newest first).
    Focus on changes that a deployer needs to act on, not internal refactors.

---

## Spinal Firmware

### `deploy` branch (STM32H7 v2)

| Date | Change | Impact | Action Required |
|------|--------|--------|----------------|
| — | *Initial entry — fill in as changes are made* | — | — |

**Template entry:**
```
| 2025-XX-XX | Changed FDCAN bitrate from 1Mbps to 500kbps | Neuron communication affected | Update Neuron firmware to match |
```

---

## Neuron Firmware

### G4 v1 board

| Date | Change | Impact | Action Required |
|------|--------|--------|----------------|
| — | *Initial entry* | — | — |

---

## ESC Firmware (AM32)

| Date | AM32 Version | Change | Impact | Action Required |
|------|-------------|--------|--------|----------------|
| — | v2.18 | *Initial tracked version* | — | — |

### AM32 Configuration Notes

If the AM32 firmware version changes, re-verify:

- [ ] DSHOT protocol version still matches Spinal output
- [ ] Motor direction settings are preserved (or re-set)
- [ ] Telemetry is enabled if used
- [ ] Throttle calibration is still valid

See [ESC Calibration](../calibration/esc_calibration.md) and [ESC Configuration](../hardware/common/esc/configuration.md).

---

## How to Log a Change

When you update firmware on a robot and something changes:

```markdown
| 2025-XX-XX | One-line description of what changed | What it affects | What others need to do |
```

Keep it brief. Link to the relevant doc page for details.
