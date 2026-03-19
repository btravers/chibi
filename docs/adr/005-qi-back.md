# ADR 005 — Qi Charging: Coil in the Back vs Feet vs Pogo Pins

**Date**: 2025-03-19

**Status**: Accepted

## Context

CHIBI must be able to recharge autonomously by returning to its dock station. Being a bipedal robot, the feet are in constant motion during walking, making the placement of the charging coil critical. The mechanism must be reliable, reproducible, and not require human intervention to align CHIBI correctly.

## Options Considered

### Option A — Qi Coil in the Feet

- Receiver coil under the soles
- Horizontal transmitter in the dock (like a standard phone charger)

**Pros**: intuitive concept (CHIBI stands "on" the charger).

**Cons**: feet move constantly during walking, alignment must be precise at each step, ankle joints complicate wiring, mechanical wear is maximum in this area.

### Option B — Pogo Pins (physical contacts)

- Pogo pin contacts in the feet or back
- Matching contacts on the dock

**Pros**: efficient energy transfer, no inductive losses.

**Cons**: very precise mechanical alignment required, contact wear, possible oxidation, mechanical complexity of the insertion mechanism.

### Option C — Qi Coil in the Back (selected)

- Qi 10W receiver coil (BQ51051B with integrated ferrite) in CHIBI's back
- Vertical Qi transmitter in the dock
- CHIBI backs up to align with the dock
- N35 6x2mm magnets in a ring (MagSafe-style) for alignment
- DRV5055 Hall sensor to confirm alignment

**Pros**: fixed and predictable contact surface (the back doesn't move during walking), reliable magnetic alignment, no mechanical wear.

**Cons**: CHIBI must turn around to charge (backs into the dock), ejection mechanism to design.

## Decision

**Qi 10W receiver coil in CHIBI's back**, with a vertical transmitter in the dock and MagSafe-style magnetic alignment.

The charging process:
1. CHIBI detects a low battery level
2. CHIBI navigates toward the dock and turns around
3. CHIBI backs up until the magnets engage
4. The DRV5055 Hall sensor confirms alignment
5. Qi charging begins
6. The display switches to charge mode (`charge-screen` plugin)

The **dock ejection mechanism question** (how CHIBI detaches from the dock after charging) is **intentionally left open**. Options under consideration include a servo-actuated cam, a spring, or a reversed electromagnet. This decision will be made during the dock's mechanical design.

## Consequences

### Positive

- **Fixed surface**: the back is the most stable part of the robot, no movement during walking
- **Reliable alignment**: N35 ring magnets ensure precise and reproducible centering
- **Electronic confirmation**: the DRV5055 Hall sensor allows firmware to verify alignment before attempting to charge
- **Contactless**: no mechanical wear, no oxidation, no dust on contacts
- **Qi standard**: mature and well-documented technology

### Negative

- **Ejection**: the mechanism to detach CHIBI from the dock remains to be designed (open question)
- **Orientation**: CHIBI must turn around (walk backwards) to charge, requiring reliable U-turn kinematics
- **Inductive losses**: Qi charging is less efficient than direct contacts (~80% vs ~95%)
- **Magnets**: N35 magnets could interfere with the IMU compass (to be verified)
