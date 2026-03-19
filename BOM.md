# CHIBI — Bill of Materials (BOM)

Complete list of required hardware, organized by category. Prices are estimates (March 2025) and may vary depending on suppliers and availability.

---

## CHIBI Brains (mobile)

| Component | Model | Role | Supplier | Phase | Price |
|-----------|-------|------|----------|-------|-------|
| MCU + display | LilyGo T4-S3 (ESP32-S3R8 + RM690B0 AMOLED 2.41") | Real-time brain, display, sensors, servos | AliExpress / LilyGo | 1 | ~35 € |
| SBC | Raspberry Pi Zero 2 W | Orchestration, vision, bridge | RPi Foundation | 4 | ~22 € |
| Camera | Pi Camera Module 3 NoIR | Vision, face/gesture detection | RPi Foundation | 4 | ~30 € |
| Camera cable | FPC cable Pi Zero → Camera | Camera connection | RPi Foundation | 4 | ~4 € |

## Dock Brains

| Component | Model | Role | Supplier | Phase | Price |
|-----------|-------|------|----------|-------|-------|
| SBC | Raspberry Pi 5 8GB | Plugin manager, MQTT, Ollama, OTA | RPi Foundation | 5 | ~95 € |
| Storage | NVMe SSD 256GB (M.2 2230) | Dock storage (plugins, AI models) | Amazon | 5 | ~35 € |
| NVMe HAT | Pi 5 NVMe HAT | M.2 adapter for Pi 5 | Pimoroni / Geekworm | 5 | ~15 € |
| Cooling | Active Cooler Pi 5 | Active cooling | RPi Foundation | 5 | ~8 € |
| Dock power supply | USB-C 5V 5A (27W) | Pi 5 + Qi transmitter power | RPi Foundation | 5 | ~15 € |

## Sensors

| Component | Model | Role | Supplier | Phase | Price |
|-----------|-------|------|----------|-------|-------|
| Capacitive touch | MPR121 breakout (12 channels) | Head touch detection | Adafruit | 1 | ~8 € |
| IMU | QMI8658 (integrated on T4-S3) | Orientation, fall, shake | — (integrated) | 1 | — |
| ToF laser | VL53L1X | Obstacle detection (4m range) | Pololu / AliExpress | 2 | ~12 € |
| ToF edge detection | VL6180X x4 | Edge detection, 4 corners under feet | Adafruit / AliExpress | 2 | ~28 € (4x7) |
| Ambient light | VEML7700 | Brightness adaptation | Adafruit / AliExpress | 2 | ~5 € |
| MEMS microphone | ICS-43434 (I2S) | Sound reaction | Adafruit / AliExpress | 2 | ~7 € |
| Temp/humidity | SHT40 Sensirion | Ambient environment | Adafruit / Mouser | 2 | ~6 € |
| Hall sensor | DRV5055 | Dock alignment detection | Mouser / DigiKey | 4 | ~3 € |

## Locomotion

| Component | Model | Role | Supplier | Phase | Price |
|-----------|-------|------|----------|-------|-------|
| Validation servos | SG90 x4 | Kinematics validation (disposable) | AliExpress | 3a | ~8 € (4x2) |
| Hip + ankle servos | Dynamixel XL330-M077-T x4 | Final bipedal locomotion | Robotis | 3b | ~100 € (4x25) |
| Head servo | Dynamixel XL330-M288-T x1 | Head movement | Robotis | 3b | ~25 € |
| Dynamixel cable | JST 3-pin cable x5 | UART daisychain bus | Robotis | 3b | ~10 € |

## Body Actuators

| Component | Model | Role | Supplier | Phase | Price |
|-----------|-------|------|----------|-------|-------|
| LEDs | SK6812 RGBW (strip or ring) | Expressive backlighting | AliExpress | 2 | ~5 € |

## Audio

| Component | Model | Role | Supplier | Phase | Price |
|-----------|-------|------|----------|-------|-------|
| Speaker | Mini speaker 8Ohm 1W (20mm) | Audio output (beeps, sounds) | AliExpress | 2 | ~3 € |
| Amplifier | MAX98357A (I2S) | Audio amplifier | Adafruit / AliExpress | 2 | ~5 € |

## Power

| Component | Model | Role | Supplier | Phase | Price |
|-----------|-------|------|----------|-------|-------|
| Battery | LiPo 1800mAh 3.7V JST-PH x2 | Mobile power (3600mAh total) | AliExpress / Amazon | 4 | ~16 € (2x8) |
| Charge management | BQ25895 breakout | LiPo charging, I2C monitoring | Adafruit / AliExpress | 4 | ~8 € |
| Regulator | LDO 3.3V (AMS1117 or equiv.) | Sensor power supply | AliExpress | 1 | ~2 € |

> **Note**: The LiPo batteries use the standard JST-PH 2mm format to allow a future upgrade to Si-C (silicon-carbon) cells without any mechanical modification to the compartments.

## Qi Charging

| Component | Model | Role | Supplier | Phase | Price |
|-----------|-------|------|----------|-------|-------|
| Qi receiver | Qi 10W module + BQ51051B (with ferrite) | Wireless charge reception | AliExpress | 4 | ~8 € |
| Qi transmitter | Qi 10W transmitter module | Charge transmission (dock) | AliExpress | 4 | ~10 € |
| Magnets | N35 neodymium 6x2mm x8 | MagSafe-style alignment | AliExpress | 4 | ~4 € |

## Connectors

| Component | Model | Role | Supplier | Phase | Price |
|-----------|-------|------|----------|-------|-------|
| I2C connectors | JST-SH 4-pin (Qwiic/Stemma QT) | I2C sensor bus | Adafruit / AliExpress | 1 | ~5 € |
| Prototype PCB | Perfboard / custom PCB | Component interconnection | AliExpress | 1 | ~5 € |
| Wires | Silicone wires AWG 26-30 (assorted) | Internal wiring | AliExpress | 1 | ~5 € |

## Electronics / Tooling

| Component | Model | Role | Supplier | Phase | Price |
|-----------|-------|------|----------|-------|-------|
| Level shifter | TXB0104 (bidirectional) | 3.3V ↔ 5V logic level conversion | Adafruit / AliExpress | 2 | ~3 € |
| Multimeter | (any basic model) | Electronics debugging | Amazon | 1 | ~15 € |

## Mechanical / 3D Printing

| Component | Model | Role | Supplier | Phase | Price |
|-----------|-------|------|----------|-------|-------|
| PLA filament | PLA 1.75mm (1kg, color of choice) | Enclosure printing | Amazon / AliExpress | 3 | ~20 € |
| Screws / inserts | M2/M3 screw kit + heat-set threaded inserts | Enclosure assembly | AliExpress | 3 | ~8 € |
| Rubber feet | Adhesive silicone pads | Floor grip | AliExpress | 3 | ~3 € |

---

## Budget Tiers

### Phase 1 Only — ~192 €

First sign of life: screen with animated eyes and touch reaction.

| Item | Detail | Cost |
|------|--------|------|
| LilyGo T4-S3 | MCU + AMOLED display | 35 € |
| MPR121 | Capacitive touch sensor | 8 € |
| 3.3V regulator | Sensor power supply | 2 € |
| Connectors | JST, PCB, wires | 15 € |
| SG90 servos x4 | Locomotion validation (Phase 3a) | 8 € |
| PLA filament | Otto chassis printing | 20 € |
| Screws / inserts | Assembly | 8 € |
| Phase 2 sensors | VL53L1X, VL6180X x4, VEML7700, ICS-43434, SHT40 | 58 € |
| LEDs + audio | SK6812, speaker, amplifier | 13 € |
| Level shifter | TXB0104 | 3 € |
| Rubber feet | Floor grip | 3 € |
| Multimeter | Debugging | 15 € |
| Miscellaneous | Component margin | 4 € |
| **Total** | | **~192 €** |

### Full CHIBI (mobile) — ~492 €

Autonomous bipedal robot with Qi charging, all sensors, Dynamixel servos.

| Item | Detail | Cost |
|------|--------|------|
| Phase 1 (above) | | 192 € |
| Dynamixel XL330-M077-T x4 | Locomotion servos | 100 € |
| Dynamixel XL330-M288-T | Head servo | 25 € |
| Dynamixel cables | Daisychain bus | 10 € |
| Pi Zero 2W + camera cable | Second brain | 26 € |
| Pi Camera 3 NoIR | Vision | 30 € |
| LiPo batteries x2 | 3600mAh total | 16 € |
| BQ25895 | Charge management | 8 € |
| Qi receiver module | Wireless charging | 8 € |
| N35 magnets x8 | Dock alignment | 4 € |
| DRV5055 | Hall sensor | 3 € |
| Custom enclosure | Additional filament | 10 € |
| Miscellaneous | Integration margin | 60 € |
| **Total** | | **~492 €** |

### Full Project with Dock — ~841 €

Complete setup: mobile CHIBI + dock station with Pi 5.

| Item | Detail | Cost |
|------|--------|------|
| Full CHIBI (above) | | 492 € |
| Raspberry Pi 5 8GB | Dock brain | 95 € |
| NVMe SSD 256GB | Storage | 35 € |
| NVMe HAT | M.2 adapter | 15 € |
| Active Cooler Pi 5 | Cooling | 8 € |
| 27W power supply | USB-C for Pi 5 | 15 € |
| Qi 10W transmitter | Dock charging | 10 € |
| Dock enclosure | 3D printing + hardware | 15 € |
| Dock miscellaneous | Wiring, margin | 156 € |
| **Total** | | **~841 €** |
