<![CDATA[# CHIBI 🤖

[![Status](https://img.shields.io/badge/status-concept-orange)](ROADMAP.md)
[![License](https://img.shields.io/badge/license-MIT-blue)](LICENSE)
[![Firmware](https://img.shields.io/badge/firmware-Rust%20%2B%20Embassy-red)](https://embassy.dev)

**CHIBI** est un compagnon de bureau physique — un petit robot bipède expressif inspiré de l'[EMO robot de LivingAI](https://living.ai/emo/) — entièrement **open source**, développé en **Rust embarqué**, et conçu comme une **plateforme extensible via plugins**.

La différence fondamentale avec EMO : CHIBI est 100% open source, entièrement en Rust, et pensé pour s'intégrer nativement à l'environnement d'un développeur (CI/CD, météo, domotique) via un système de plugins communautaires.

---

## Fonctionnalités clés

- **Expressions paramétriques** — yeux, sourcils et bouche générés dynamiquement (pas de sprites), moteur émotionnel à deux axes continus (valence + énergie)
- **Locomotion bipède** — architecture Otto DIY, 4 servos Dynamixel XL330 (hanches + chevilles) + 1 servo tête, bus UART daisychain avec télémétrie
- **Écran AMOLED** — RM690B0 2.41" (600×450, 16.7M couleurs) intégré sur la LilyGo T4-S3, noirs parfaits pour les expressions
- **Système de plugins** — API REST + WebSocket, n'importe quel langage, fusion des états par priorité
- **Charge sans fil Qi** — bobine dans le dos, alignement magnétique MagSafe, dock avec Pi 5
- **Capteurs multiples** — tactile capacitif, IMU, ToF laser, anti-chute, lumière ambiante, micro MEMS, température/humidité

---

## Architecture hardware

CHIBI se compose de deux éléments : le **robot mobile** et la **dock station**.

```
┌─────────────────────────────────────────────────────────┐
│                    CHIBI (mobile)                        │
│                                                         │
│  ┌──────────────────────┐   ┌────────────────────────┐  │
│  │   LilyGo T4-S3       │   │   Raspberry Pi Zero 2W │  │
│  │   (ESP32-S3 + AMOLED) │   │   (orchestration)      │  │
│  │                       │   │                         │  │
│  │  • Firmware Rust      │   │  • Vision (Pi Camera)   │  │
│  │  • Affichage RM690B0  │◄─►│  • Bridge ESP↔Pi 5     │  │
│  │  • Capteurs (I2C/SPI) │UART│  • Audio processing    │  │
│  │  • Servos Dynamixel   │   │                         │  │
│  │  • API REST/WebSocket │   │                         │  │
│  │  • Wi-Fi              │   │                         │  │
│  └──────────────────────┘   └────────────────────────┘  │
│                                                         │
│  Batterie : 2× LiPo 1800mAh dans les jambes (3600mAh)  │
│  Charge : Qi 10W (bobine dans le dos)                   │
│  Servos : 4× XL330-M077-T + 1× XL330-M288-T (UART)    │
└──────────────────────────────┬──────────────────────────┘
                               │ Wi-Fi
┌──────────────────────────────▼──────────────────────────┐
│                    Dock Station                          │
│                                                         │
│  ┌─────────────────────────────────────────────────┐    │
│  │   Raspberry Pi 5 8GB + SSD NVMe 256GB           │    │
│  │                                                  │    │
│  │  • Plugin Manager       • Serveur OTA           │    │
│  │  • Broker MQTT          • Ollama (LLM local)    │    │
│  │  • Communication Wi-Fi avec CHIBI               │    │
│  └─────────────────────────────────────────────────┘    │
│                                                         │
│  Émetteur Qi vertical (aligné avec le dos de CHIBI)     │
└─────────────────────────────────────────────────────────┘
```

---

## Architecture plugin

Le coeur de CHIBI est son **système de plugins**. Un plugin est un processus autonome dans n'importe quel langage qui communique avec CHIBI via HTTP.

Deux niveaux d'API sont exposés :

### API sémantique (haut niveau)

Pour les plugins qui veulent influencer le comportement de CHIBI sans gérer les détails :

```http
POST /emotion/event    { "type": "success" | "failure" | "warning" }
POST /notification     { "message": "Tests passed", "severity": "info" }
POST /display/text     { "text": "Build OK", "duration": 3000 }
GET  /state            → { "emotion": {...}, "battery": {...}, "sensors": {...} }
```

### API primitive (bas niveau)

Pour le contrôle direct des actuateurs et capteurs :

```http
POST /display/expression  { "eyes": "happy", "intensity": 0.8 }
POST /motion/dance        { "pattern": "wiggle", "bpm": 120 }
POST /led/color           { "r": 255, "g": 100, "b": 0, "fade": 500 }
GET  /sensors             → { "touch": {...}, "proximity": {...}, "light": {...} }
```

### Fusion par priorité

Chaque plugin déclare une **priorité** (0–100) et une **durée d'effet**. Le firmware fusionne les états actifs par ordre de priorité. Exemple : si le plugin CI/CD (priorité 80) émet `failure` pendant 30s et le plugin météo (priorité 20) émet `sad` en continu, CHIBI affiche `failure` 30s puis revient à `sad`.

---

## Stack technique

| Composant | Technologie | Rôle |
|-----------|-------------|------|
| Firmware | Rust + Embassy (no_std, async) | Contrôle temps réel ESP32-S3 |
| Affichage | `rm690b0-rs` + `embedded-graphics` | Rendu expressions paramétriques |
| Serveur HTTP | `picoserve` | API REST embarquée (style Axum) |
| Networking | `embassy-net` | Wi-Fi ESP32-S3 |
| Servos | Dynamixel 2.0 (driver Rust custom) | Bus UART daisychain |
| Companion app | Tauri + Leptos | App desktop configuration |
| Dock | Raspberry Pi 5 + Mosquitto + Ollama | Orchestration, MQTT, IA locale |

---

## Plugins officiels prévus

| Plugin | Description |
|--------|-------------|
| `github-ci` | Réagit aux GitHub Actions (succès, échec, en cours) |
| `weather` | L'humeur de CHIBI reflète la météo locale |
| `pomodoro` | Compagnon de sessions Pomodoro |
| `home-assistant` | Intégration domotique |
| `mqtt-bridge` | Bridge IoT générique |
| `dev-mood` | Connecté à l'IDE (erreurs, builds) |
| `charge-screen` | Écran d'informations pendant la charge |

---

## Structure du projet

```
chibi/
├── chibi-firmware/    # Firmware Rust (ESP32-S3, Embassy)
├── chibi-api/         # Spécification OpenAPI + types partagés
├── chibi-plugins/     # Registry plugins officiels
├── chibi-cli/         # CLI (chibi install, chibi enable...)
├── chibi-app/         # Companion app (Tauri + Leptos)
├── chibi-hardware/    # Schémas, BOM, fichiers CAO 3D
└── docs/adr/          # Architecture Decision Records
```

---

## Documentation

- **[ROADMAP.md](ROADMAP.md)** — Roadmap en 8 phases, de l'allumage des yeux à l'IA locale
- **[BOM.md](BOM.md)** — Liste complète du matériel avec prix indicatifs et niveaux de budget
- **[docs/adr/](docs/adr/)** — 6 Architecture Decision Records documentant les choix techniques

---

## Références

- [Embassy](https://embassy.dev) — Framework async Rust pour embedded
- [rm690b0-rs](https://github.com/theembeddedrustacean/rm690b0-rs) — Driver Rust pour l'écran RM690B0
- [picoserve](https://docs.rs/picoserve) — Serveur HTTP embarqué inspiré d'Axum
- [Otto DIY](https://www.printables.com/model/31955-otto-diy-build-your-own-robot) — Base mécanique bipède open source
- [EMO Robot](https://living.ai/emo/) — Inspiration principale
- [SANGI](https://github.com/umersanii/SANGI) — Projet similaire de desktop pet

---

## Licence

[MIT](LICENSE)
]]>