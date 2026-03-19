# CHIBI

[![Status](https://img.shields.io/badge/status-concept-orange)](ROADMAP.md)
[![License](https://img.shields.io/badge/license-MIT-blue)](LICENSE)
[![Firmware](https://img.shields.io/badge/firmware-Rust%20%2B%20Embassy-red)](https://embassy.dev)

**CHIBI** is a physical desk companion — a small, expressive bipedal robot inspired by the [EMO robot by LivingAI](https://living.ai/emo/) — fully **open source**, built with **embedded Rust**, and designed as an **extensible plugin platform**.

The key difference with EMO: CHIBI is 100% open source, entirely written in Rust, and built to natively integrate with a developer's environment (CI/CD, weather, home automation) through a community-driven plugin system.

---

## Key Features

- **Parametric expressions** — eyes, eyebrows and mouth generated dynamically (no sprites), emotion engine based on two continuous axes (valence + arousal)
- **Bipedal locomotion** — Otto DIY architecture, 4 Dynamixel XL330 servos (hips + ankles) + 1 head servo, UART daisychain bus with telemetry
- **AMOLED display** — RM690B0 2.41" (600x450, 16.7M colors) integrated on the LilyGo T4-S3, perfect blacks for expressions
- **Plugin system** — REST + WebSocket API, any language, priority-based state merging
- **Qi wireless charging** — coil in the back, MagSafe-style magnetic alignment, dock with Pi 5
- **Multiple sensors** — capacitive touch, IMU, ToF laser, edge detection, ambient light, MEMS microphone, temperature/humidity

---

## Hardware Architecture

CHIBI consists of two elements: the **mobile robot** and the **dock station**.

```
┌─────────────────────────────────────────────────────────┐
│                    CHIBI (mobile)                        │
│                                                         │
│  ┌──────────────────────┐   ┌────────────────────────┐  │
│  │   LilyGo T4-S3       │   │   Raspberry Pi Zero 2W │  │
│  │   (ESP32-S3 + AMOLED) │   │   (orchestration)      │  │
│  │                       │   │                         │  │
│  │  • Rust firmware      │   │  • Vision (Pi Camera)   │  │
│  │  • RM690B0 display    │◄─►│  • ESP↔Pi 5 bridge     │  │
│  │  • Sensors (I2C/SPI)  │UART│  • Audio processing    │  │
│  │  • Dynamixel servos   │   │                         │  │
│  │  • REST/WebSocket API │   │                         │  │
│  │  • Wi-Fi              │   │                         │  │
│  └──────────────────────┘   └────────────────────────┘  │
│                                                         │
│  Battery: 2x LiPo 1800mAh in the legs (3600mAh total)  │
│  Charging: Qi 10W (coil in the back)                    │
│  Servos: 4x XL330-M077-T + 1x XL330-M288-T (UART)     │
└──────────────────────────────┬──────────────────────────┘
                               │ Wi-Fi
┌──────────────────────────────▼──────────────────────────┐
│                    Dock Station                          │
│                                                         │
│  ┌─────────────────────────────────────────────────┐    │
│  │   Raspberry Pi 5 8GB + NVMe SSD 256GB           │    │
│  │                                                  │    │
│  │  • Plugin Manager       • OTA Server            │    │
│  │  • MQTT Broker          • Ollama (local LLM)    │    │
│  │  • Wi-Fi communication with CHIBI               │    │
│  └─────────────────────────────────────────────────┘    │
│                                                         │
│  Vertical Qi transmitter (aligned with CHIBI's back)    │
└─────────────────────────────────────────────────────────┘
```

---

## Plugin Architecture

The heart of CHIBI is its **plugin system**. A plugin is a standalone process in any language that communicates with CHIBI over HTTP.

Two API levels are exposed:

### Semantic API (high level)

For plugins that want to influence CHIBI's behavior without managing low-level details:

```http
POST /emotion/event    { "type": "success" | "failure" | "warning" }
POST /notification     { "message": "Tests passed", "severity": "info" }
POST /display/text     { "text": "Build OK", "duration": 3000 }
GET  /state            → { "emotion": {...}, "battery": {...}, "sensors": {...} }
```

### Primitive API (low level)

For direct control of actuators and sensors:

```http
POST /display/expression  { "eyes": "happy", "intensity": 0.8 }
POST /motion/dance        { "pattern": "wiggle", "bpm": 120 }
POST /led/color           { "r": 255, "g": 100, "b": 0, "fade": 500 }
GET  /sensors             → { "touch": {...}, "proximity": {...}, "light": {...} }
```

### Priority-Based Merging

Each plugin declares a **priority** (0–100) and an **effect duration**. The firmware merges active states by priority order. Example: if the CI/CD plugin (priority 80) emits `failure` for 30s and the weather plugin (priority 20) emits `sad` continuously, CHIBI shows `failure` for 30s then falls back to `sad`.

---

## Tech Stack

| Component | Technology | Role |
|-----------|------------|------|
| Firmware | Rust + Embassy (no_std, async) | Real-time control on ESP32-S3 |
| Display | `rm690b0-rs` + `embedded-graphics` | Parametric expression rendering |
| HTTP Server | `picoserve` | Embedded REST API (Axum-style) |
| Networking | `embassy-net` | ESP32-S3 Wi-Fi |
| Servos | Dynamixel 2.0 (custom Rust driver) | UART daisychain bus |
| Companion app | Tauri + Leptos | Desktop configuration app |
| Dock | Raspberry Pi 5 + Mosquitto + Ollama | Orchestration, MQTT, local AI |

---

## Planned Official Plugins

| Plugin | Description |
|--------|-------------|
| `github-ci` | Reacts to GitHub Actions (success, failure, in progress) |
| `weather` | CHIBI's mood reflects local weather |
| `pomodoro` | Pomodoro session companion |
| `home-assistant` | Home automation integration |
| `mqtt-bridge` | Generic IoT bridge |
| `dev-mood` | Connected to the IDE (errors, builds) |
| `charge-screen` | Information display while charging |

---

## Project Structure

```
chibi/
├── chibi-firmware/    # Rust firmware (ESP32-S3, Embassy)
├── chibi-api/         # OpenAPI specification + shared types
├── chibi-plugins/     # Official plugin registry
├── chibi-cli/         # CLI (chibi install, chibi enable...)
├── chibi-app/         # Companion app (Tauri + Leptos)
├── chibi-hardware/    # Schematics, BOM, 3D CAD files
└── docs/adr/          # Architecture Decision Records
```

---

## Documentation

- **[ROADMAP.md](ROADMAP.md)** — 8-phase roadmap, from lighting up the eyes to local AI
- **[BOM.md](BOM.md)** — Complete bill of materials with estimated prices and budget tiers
- **[docs/adr/](docs/adr/)** — 6 Architecture Decision Records documenting key technical choices

---

## References

- [Embassy](https://embassy.dev) — Async Rust framework for embedded
- [rm690b0-rs](https://github.com/theembeddedrustacean/rm690b0-rs) — Rust driver for the RM690B0 display
- [picoserve](https://docs.rs/picoserve) — Embedded HTTP server inspired by Axum
- [Otto DIY](https://www.printables.com/model/31955-otto-diy-build-your-own-robot) — Open source bipedal mechanical base
- [EMO Robot](https://living.ai/emo/) — Main inspiration
- [SANGI](https://github.com/umersanii/SANGI) — Similar desktop pet project

---

## License

[MIT](LICENSE)
