# ADR 003 — Servos: Dynamixel 2.0 UART Bus vs Individual PWM

**Date**: 2025-03-19

**Status**: Accepted

## Context

CHIBI is a bipedal robot with 4 locomotion servos (2 hips + 2 ankles) and 1 head servo, totaling 5 servos. The choice of communication protocol with the servos impacts wiring, feedback capabilities (telemetry), and motion control quality.

The mechanical architecture is based on Otto DIY, an open source bipedal robot whose STL files are available on Printables.

## Options Considered

### Option A — Individual PWM with SG90/MG90S Servos

- 5 individual PWM wires from the ESP32-S3 to each servo
- Cheap analog servos (SG90 ~2€, MG90S ~5€)
- Open-loop control (no position feedback)

**Pros**: very cheap, simple to program, abundance of libraries and examples.

**Cons**: 5 GPIOs occupied, no position or torque feedback (open loop), star wiring complex in a small enclosure, impossible to detect mechanical stalling, wear not detectable.

### Option B — Dynamixel 2.0 UART Bus (selected)

- 5 Dynamixel XL330 servos connected in daisychain on a single UART bus
- Dynamixel 2.0 protocol (half-duplex UART, ID-based addressing)
- Full telemetry: exact position, speed, torque, temperature, voltage

**Pros**: single cable for all servos (daisychain), real-time position and torque feedback, stall and overheating detection, precise closed-loop control.

**Cons**: more expensive servos (~25€ each), Dynamixel 2.0 protocol must be implemented in Rust (no existing crate), half-duplex UART requires communication direction management.

### Option C — I2C with PCA9685 Servos

- PCA9685 PWM driver on I2C bus, controlling standard servos
- Single I2C bus for the driver, but servos still open-loop

**Rejected**: solves the GPIO count problem but not the position feedback issue. Adds a component (PCA9685) without providing telemetry.

## Decision

**Dynamixel XL330 on UART daisychain bus** with Dynamixel 2.0 protocol.

- 4x XL330-M077-T (1.0 Nm) for hips and ankles
- 1x XL330-M288-T (0.34 Nm) for the head (lower torque sufficient)
- Dynamixel 2.0 protocol implementation in Rust as an **open source contribution**

Initial kinematics validation (Phase 3a) uses cheap SG90 servos on the Otto DIY chassis, before migrating to Dynamixel (Phase 3b).

## Consequences

### Positive

- **Simplified wiring**: a single UART cable runs through the body in daisychain instead of 5 individual wires
- **Telemetry**: temperature, torque and exact position accessible in real time
- **Problem detection**: mechanical stalling, overheating, and wear detectable by the firmware
- **Precise control**: closed-loop with position feedback, smoother movements
- **Open source contribution**: the Dynamixel 2.0 Rust driver will benefit the entire Rust embedded community

### Negative

- **Cost**: 5 Dynamixel servos = ~135€ vs ~10€ for 5 SG90s
- **Development**: Dynamixel 2.0 protocol must be implemented from scratch in Rust (no existing crate)
- **Half-duplex**: UART communication direction management adds complexity to the driver
- **Supply**: XL330s are sometimes out of stock at Robotis
