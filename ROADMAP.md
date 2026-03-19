# CHIBI — Roadmap

Progressive 8-phase roadmap, from lighting up the eyes to on-device AI.

---

## Phase 1 — Light Up the Eyes

**Goal**: First sign of life — display animated eyes on the AMOLED screen.

- Set up Rust + Embassy project for ESP32-S3
- Integrate the `rm690b0-rs` driver for the RM690B0 AMOLED display (LilyGo T4-S3)
- PSRAM framebuffer (8MB) with DMA transfers
- Render parametric eyes with `embedded-graphics` (aperture, pupil position, tilt)
- Basic animations: blinking, random tracking, idle
- Integrate MPR121 capacitive touch sensor (12 channels, head) — react to touch
- Minimal emotion engine: valence (sad ↔ happy) and arousal (calm ↔ excited) axes

**Hardware**: LilyGo T4-S3, MPR121 breakout (Adafruit)

---

## Phase 2 — Sensors & Reactivity

**Goal**: CHIBI perceives its environment and reacts to it.

- VL53L1X (ToF laser, 4m range) — obstacle and presence detection
- VL6180X x4 — edge detection, mounted at all 4 corners under the feet
- VEML7700 — ambient light adaptation (darker expressions at night)
- ICS-43434 MEMS I2S — sound reaction (claps, voice)
- SHT40 Sensirion — ambient temperature and humidity
- QMI8658 IMU (integrated on T4-S3) — orientation, fall, and shake detection
- SK6812 LEDs — expressive backlighting synchronized with emotions
- Extended emotion engine: more reactions, smooth transitions between states

**Hardware**: VL53L1X, VL6180X x4, VEML7700, ICS-43434, SHT40

---

## Phase 3 — Bipedal Locomotion

**Goal**: CHIBI walks on two legs.

### Step 3a — Validation with SG90

- Assemble Otto DIY chassis (STL files from Printables)
- 4 SG90 servos (PWM) to validate walking kinematics
- Basic animations: walk forward/backward, rotation, simple dance
- Validate balance and center of gravity

### Step 3b — Dynamixel Migration

- Replace SG90s with 4x Dynamixel XL330-M077-T (hips + ankles)
- Add Dynamixel XL330-M288-T head servo
- Implement Dynamixel 2.0 protocol in Rust (open source contribution)
  - UART daisychain bus communication
  - Telemetry readback: temperature, torque, exact position
  - Commands: goal position, moving speed, torque enable
- Improved walking with position and torque feedback

**Hardware**: Otto DIY chassis (3D printed), SG90 x4 (validation), then XL330-M077-T x4 + XL330-M288-T x1

---

## Phase 4 — Qi Charging + Dock MVP

**Goal**: CHIBI charges wirelessly and has a custom enclosure.

- Design custom enclosure in Fusion 360 (based on Otto DIY)
- Battery compartments for 2x LiPo 1800mAh in the legs (standard JST-PH 2mm format)
- BQ25895 integration (charge management, I2C monitoring)
- Qi 10W receiver coil (BQ51051B) in the back
- N35 6x2mm magnets (MagSafe-style alignment)
- DRV5055 Hall sensor — dock alignment confirmation
- Raspberry Pi Zero 2W integration in the enclosure
- Pi Camera Module 3 NoIR — basic vision
- Dock MVP: vertical Qi transmitter, power supply

**Hardware**: LiPo batteries x2, BQ25895, Qi coil + BQ51051B, N35 magnets, DRV5055, Pi Zero 2W, Pi Camera 3 NoIR

---

## Phase 5 — Wi-Fi + REST API

**Goal**: CHIBI is controllable over the network.

- Embedded HTTP server with `picoserve` on the ESP32-S3
- `embassy-net` for Wi-Fi management
- Semantic API: `/emotion/event`, `/notification`, `/display/text`, `/state`
- Primitive API: `/display/expression`, `/motion/dance`, `/led/color`, `/sensors`
- WebSocket for real-time state streaming
- UART communication between ESP32-S3 and Pi Zero 2W
- Full dock: Raspberry Pi 5 8GB + NVMe SSD 256GB
- MQTT broker (Mosquitto) on the Pi 5
- OTA server on the Pi 5

**Hardware**: Raspberry Pi 5 8GB, NVMe SSD 256GB, active cooler, dock enclosure

---

## Phase 6 — Plugin Ecosystem

**Goal**: CHIBI becomes an extensible platform.

- Plugin manager on the Pi 5 (discovery, installation, updates)
- Priority system and state merging between plugins
- Official plugins:
  - `github-ci` — reactions to GitHub Actions
  - `weather` — mood based on local weather
  - `pomodoro` — session companion
  - `home-assistant` — home automation integration
  - `mqtt-bridge` — generic IoT bridge
  - `dev-mood` — connected to the IDE
  - `charge-screen` — information display while charging
- `chibi-cli` — command-line tool (`chibi install`, `chibi enable`, `chibi status`)
- Plugin registry (manifest format, validation)
- Companion app with Tauri + Leptos (configuration, visualization, logs)

---

## Phase 7 — Memory & Evolving Personality

**Goal**: CHIBI develops a unique personality over time.

- Flash persistence (NVS) for personality parameters
- Interaction history (petting frequency, usage patterns)
- Progressive adaptation of reactions (CHIBI "learns" preferences)
- Configurable temperament parameters (shy ↔ outgoing, calm ↔ nervous)
- Mood logs viewable through the companion app

---

## Phase 8 — Local AI

**Goal**: CHIBI understands and interacts vocally.

- Wake word detection on the Pi Zero 2W
- Vision: face detection, gesture recognition (Pi Camera)
- Local LLM via Ollama on the Pi 5 (basic conversation, personality)
- Local text-to-speech for voice responses
- Integration with the emotion engine (the LLM influences mood)

---

## Open Questions

These questions will be resolved during development:

- **Dock ejection mechanism** — How does CHIBI detach from the dock after charging? Servo-actuated cam, spring, electromagnet? To be decided during the mechanical design of the dock.
- **Exact battery compartment dimensions** — The precise cavity dimensions in the legs depend on the final mechanical design. The JST-PH 2mm format is fixed, but the exact geometry remains to be defined.
- **OTA protocol** — The over-the-air firmware update mechanism for the ESP32-S3 needs to be defined. Options: HTTP pull from the Pi 5, push via MQTT, or integration with a dedicated service.

---

## Architecture Decision Records (ADRs)

Key architecture decisions are documented in [`docs/adr/`](docs/adr/):

| ADR | Title |
|-----|-------|
| [001](docs/adr/001-plugin-api.md) | Plugin API: REST + WebSocket vs MQTT Only |
| [002](docs/adr/002-emotion-engine.md) | Emotion Engine: Continuous Axes vs Discrete States |
| [003](docs/adr/003-dynamixel-uart.md) | Servos: Dynamixel 2.0 UART Bus vs Individual PWM |
| [004](docs/adr/004-battery-legs.md) | Battery: Two LiPo Cells in the Legs |
| [005](docs/adr/005-qi-back.md) | Qi Charging: Coil in the Back vs Feet vs Pogo Pins |
| [006](docs/adr/006-lilygo-t4s3.md) | Board: LilyGo T4-S3 vs Separate Components |
