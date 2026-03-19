# ADR 001 — Plugin API: REST + WebSocket vs MQTT Only

**Date**: 2025-03-19

**Status**: Accepted

## Context

CHIBI is designed as an extensible platform through plugins. A plugin is a standalone process, written in any language, that controls the robot's behavior (emotions, movements, display). We need to choose the communication protocol between plugins and the embedded firmware on the ESP32-S3.

The system must be accessible to the widest range of developers, work on a local network, and support both one-off commands (triggering an emotion) and continuous streaming (real-time sensor state).

## Options Considered

### Option A — MQTT Only

- Mosquitto broker on the dock's Pi 5
- Plugins publish to topics (`chibi/emotion/event`, `chibi/motion/dance`)
- Firmware subscribes to relevant topics
- State streaming via publish topics (`chibi/state`, `chibi/sensors`)

**Pros**: lightweight protocol, well suited to IoT, native pub/sub, natural decoupling.

**Cons**: requires a broker (dock dependency), MQTT is less familiar than HTTP for most developers, no native request/response semantics, more complex debugging (no `curl`).

### Option B — REST + WebSocket (selected)

- Embedded HTTP server directly on the ESP32-S3 via `picoserve`
- Plugins send standard HTTP requests (`POST /emotion/event`, `GET /state`)
- WebSocket for real-time state streaming
- MQTT kept only for Home Assistant integration

**Pros**: HTTP is universal (every language has an HTTP client), testable with `curl`, clear request/response semantics, no broker dependency, works even without the dock.

**Cons**: slightly heavier than MQTT on the ESP32, WebSocket must be maintained on the firmware side.

### Option C — gRPC

- Communication via Protocol Buffers and gRPC

**Rejected**: too heavy for a microcontroller, virtually nonexistent embedded ecosystem.

## Decision

**REST + WebSocket** as the primary plugin API protocol, with `picoserve` as the embedded HTTP server on the ESP32-S3. MQTT remains used for Home Assistant communication via the `mqtt-bridge` plugin, but is not the primary protocol.

Two API levels are defined:
- **Semantic API** (high level): business events (`/emotion/event`, `/notification`)
- **Primitive API** (low level): direct actuator control (`/display/expression`, `/motion/dance`)

## Consequences

### Positive

- Any developer can write a plugin with a simple HTTP client
- Easy debugging with `curl` or Postman
- CHIBI works in standalone mode (without dock) — the HTTP server is embedded
- `picoserve` is inspired by Axum, a familiar API for Rust developers

### Negative

- The HTTP server on ESP32-S3 consumes more resources than an MQTT client
- WebSocket adds complexity to the firmware
- Plugins need to know CHIBI's IP address (solved by mDNS)
