---
tags:
  - template
---

# [Robot Name] — Available Controllers

This page lists all control algorithms available for [Robot Name], maintained by different team members.

---

## Controller Overview

| Controller | Type | Status | Maintainer | Branch / Repo |
|-----------|------|--------|-----------|---------------|
| [LQI (Default)](lqi.md) | Under-actuated LQI | :material-check-circle:{ style="color:green" } Stable | — | `aerial_robot/deploy` |
| [Template Controller](template_controller.md) | — | :material-circle-outline: Stub | — | — |

**Status legend:**

- :material-check-circle:{ style="color:green" } **Stable** — tested on real machine, safe to use
- :material-progress-clock:{ style="color:orange" } **Experimental** — tested in simulation, real-machine untested
- :material-alert:{ style="color:red" } **Broken** — known issues, do not use
- :material-archive:{ style="color:gray" } **Archived** — deprecated, kept for reference

---

## How to Add a Controller

1. Create a subdirectory: `robots/[name]/controllers/<your_controller>/`
2. At minimum, create `overview.md` with:
   - What the controller does (control law, reference)
   - Which code branch or external repo to use
   - Simulation launch command
   - Known limitations
3. Add a row to the table above
4. Add nav entries to `mkdocs.yml`

---

## Choosing a Controller

<!-- Describe when to use which controller. For example:
     - Use LQI for standard flight tasks
     - Use MPC for trajectory tracking with obstacle avoidance
     - Use fully-actuated for manipulation tasks -->

*TODO: Add guidance on controller selection for this robot.*
