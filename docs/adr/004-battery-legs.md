# ADR 004 — Battery: Two LiPo Cells in the Legs

**Date**: 2025-03-19

**Status**: Accepted

## Context

CHIBI is a battery-powered autonomous bipedal robot. The switch from a wheeled architecture to a bipedal one (Otto DIY) changed the power and balance constraints. A bipedal robot is inherently less stable than a wheeled robot — the center of gravity must be as low as possible to facilitate walking.

The target capacity is approximately 3600mAh (3.7V) to allow several hours of autonomy with the AMOLED display, sensors, Wi-Fi, and servos.

## Options Considered

### Option A — Single Battery in the Body

- A single LiPo pack (3600mAh) housed in the torso
- Center of gravity at the top of the robot

**Pros**: simple to integrate, single connector, trivial monitoring.

**Cons**: high center of gravity (bipedal instability), torso crowding (space already occupied by boards and sensors), custom pack required.

### Option B — Two Cells in the Legs (selected)

- 2x LiPo 1800mAh 3.7V (standard JST-PH 2mm format) in dedicated compartments in the legs
- Cells connected in parallel (3600mAh total, 3.7V)
- BQ25895 for charge management and I2C monitoring

**Pros**: very low center of gravity (stability), symmetric mass distribution, standard format (easy future upgrade), frees up torso space.

**Cons**: two connectors to wire, compartments to design in the legs, parallel configuration requires matched cells.

### Option C — Battery in the Feet

- Flat cells in the soles of the feet

**Rejected**: the feet are the most mobile and mechanically stressed parts. Wiring would be complex and fragile at the ankle joints.

## Decision

**Two LiPo 1800mAh 3.7V cells in standard JST-PH 2mm format**, housed in CHIBI's legs, connected in parallel to provide 3600mAh total.

The BQ25895 manages charging of both cells in parallel and monitors their state (voltage, current, temperature) via I2C.

The leg compartments are sized to accommodate standard format cells, which will allow a future **upgrade to Si-C cells** (silicon-carbon, higher energy density) of the same form factor without mechanical modification.

## Consequences

### Positive

- **Low center of gravity**: batteries in the legs weigh down the bottom of the robot, significantly improving bipedal stability
- **Symmetric distribution**: equal mass in each leg, no lateral imbalance
- **Standard format**: JST-PH 2mm cells are widely available and interchangeable
- **Future upgrade**: switching to Si-C cells (higher density) will require no mechanical modification
- **Freed torso space**: more room for boards, sensors, and wiring

### Negative

- **Wiring**: two connectors to route from the legs to the BQ25895 in the torso, through the joints
- **Cell matching**: the two parallel cells should ideally be from the same batch for consistent behavior
- **Mechanical design**: the leg compartments add complexity to the 3D design (exact dimensions to be determined)
