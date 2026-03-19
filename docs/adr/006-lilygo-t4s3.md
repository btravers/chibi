# ADR 006 — Board: LilyGo T4-S3 vs Separate Components

**Date**: 2025-03-19

**Status**: Accepted

## Context

The target display for CHIBI's expressions is the RM690B0, a 2.41" AMOLED with 600x450 pixel resolution and 16.7M colors. Its perfect blacks are essential: the black background is "free" (pixels off = zero power consumption), making the eyes stand out spectacularly.

We need to choose between using an integrated board that combines the MCU and display, or assembling the components separately (ESP32-S3 DevKit + manually connected display).

## Options Considered

### Option A — Separate Components (ESP32-S3 DevKit + display)

- ESP32-S3-DevKitC-1 (~8€) + bare RM690B0 display (~25€)
- Manual QSPI wiring between the board and the display
- Possibility to use a different display (round GC9A01, ST7789, etc.)

**Pros**: display choice flexibility, individually replaceable components, potentially cheaper.

**Cons**: complex QSPI wiring (6+ high-frequency wires), no guarantee the Rust driver works without the reference board, no integrated IMU, more space occupied in the enclosure.

### Option B — LilyGo T4-S3 (selected)

- Integrated board: ESP32-S3R8 + RM690B0 AMOLED 2.41" + QSPI interface
- QMI8658 IMU integrated on the board
- 8MB PSRAM for the framebuffer
- Schematics and pinout documented by LilyGo

**Pros**: everything is wired and tested, the `rm690b0-rs` driver specifically targets this board, PSRAM + DMA framebuffer already configured in examples, IMU included for free.

**Cons**: single vendor dependency (LilyGo), less flexibility if wanting to change the display.

### Option C — Other Integrated Board (ESP32-S3 + GC9A01)

- Boards with round GC9A01 display (e.g., Waveshare ESP32-S3 1.28")

**Rejected**: the GC9A01 is a round 240x240 pixel display, too small and too low resolution to display two expressive eyes side by side. The rectangular format of the RM690B0 (600x450) is required for the target design (two eyes on a single screen, EMO-style).

## Decision

**LilyGo T4-S3** as the main firmware board for CHIBI.

Key reasons:
1. The Rust driver `rm690b0-rs` (maintained by The Embedded Rustacean) **specifically targets this board** — examples, PSRAM/DMA configuration, and pinouts are documented for the T4-S3
2. The RM690B0 AMOLED display is **already connected via QSPI** — no high-frequency wiring required
3. The **QMI8658 IMU is integrated** — one fewer sensor to wire and house in the enclosure
4. The **PSRAM framebuffer** (8MB) with DMA transfers is already configured in the driver examples
5. **Schematics are available** — no reverse engineering needed

## Consequences

### Positive

- **~2€ savings** compared to separate components (T4-S3 ~35€ vs DevKit ~8€ + bare display ~25€ + connectors ~4€)
- **Less wiring**: QSPI display and IMU already connected, space savings in the enclosure
- **Direct migration** to `rm690b0-rs`: the driver is tested and maintained for this exact board
- **Free IMU**: the integrated QMI8658 provides accelerometer and gyroscope without additional components
- **Reliability**: high-frequency QSPI connection made in factory, no risk of bad contacts

### Negative

- **Vendor dependency**: if LilyGo discontinues the T4-S3, migration to another solution will be needed
- **Reduced flexibility**: changing the display would require changing the board
- **Form factor**: the T4-S3's size and shape constrain the enclosure design
