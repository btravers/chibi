<![CDATA[# CHIBI — Roadmap

Roadmap en 8 phases progressives, de l'allumage des yeux à l'IA locale embarquée.

---

## Phase 1 — Allumer les yeux

**Objectif** : Premier signe de vie — afficher des yeux animés sur l'écran AMOLED.

- Mise en place du projet Rust + Embassy pour ESP32-S3
- Intégration du driver `rm690b0-rs` pour l'écran AMOLED RM690B0 (LilyGo T4-S3)
- Framebuffer en PSRAM (8MB) avec transferts DMA
- Rendu des yeux paramétriques avec `embedded-graphics` (ouverture, position pupille, inclinaison)
- Animations de base : clignement, suivi aléatoire, idle
- Intégration du capteur tactile MPR121 (12 points capacitifs, tête) — réaction au toucher
- Moteur émotionnel minimal : axes valence (triste ↔ heureux) et énergie (calme ↔ excité)

**Matériel** : LilyGo T4-S3, MPR121 breakout (Adafruit)

---

## Phase 2 — Capteurs & réactivité

**Objectif** : CHIBI perçoit son environnement et y réagit.

- VL53L1X (ToF laser, portée 4m) — détection obstacles et présence
- VL6180X ×4 — anti-chute, montés aux 4 coins sous les pieds
- VEML7700 — adaptation à la lumière ambiante (expressions plus sombres la nuit)
- ICS-43434 MEMS I2S — réaction aux sons (clap, voix)
- SHT40 Sensirion — température et humidité ambiante
- QMI8658 IMU (intégré T4-S3) — détection orientation, chute, secousses
- LEDs SK6812 — rétroéclairage expressif synchronisé aux émotions
- Extension du moteur émotionnel : plus de réactions, transitions fluides entre états

**Matériel** : VL53L1X, VL6180X ×4, VEML7700, ICS-43434, SHT40

---

## Phase 3 — Locomotion bipède

**Objectif** : CHIBI marche sur deux jambes.

### Étape 3a — Validation avec SG90

- Assemblage du châssis Otto DIY (STL de Printables)
- 4 servos SG90 (PWM) pour valider la cinématique de marche
- Animations de base : marche avant/arrière, rotation, danse simple
- Validation de l'équilibre et du centre de gravité

### Étape 3b — Migration Dynamixel

- Remplacement des SG90 par 4× Dynamixel XL330-M077-T (hanches + chevilles)
- Ajout du servo tête Dynamixel XL330-M288-T
- Implémentation du protocole Dynamixel 2.0 en Rust (contribution open source)
  - Communication bus UART daisychain
  - Lecture télémétrie : température, couple, position exacte
  - Commandes : goal position, moving speed, torque enable
- Marche améliorée avec retour de position et couple

**Matériel** : Châssis Otto DIY (impression 3D), SG90 ×4 (validation), puis XL330-M077-T ×4 + XL330-M288-T ×1

---

## Phase 4 — Charge Qi + Dock MVP

**Objectif** : CHIBI se recharge sans fil et a un boîtier custom.

- Conception du boîtier custom dans Fusion 360 (basé sur Otto DIY)
- Logements pour 2× LiPo 1800mAh dans les jambes (format JST-PH 2mm standard)
- Intégration du BQ25895 (gestion charge, monitoring I2C)
- Bobine réceptrice Qi 10W (BQ51051B) dans le dos
- Aimants N35 6×2mm (alignement style MagSafe)
- Capteur Hall DRV5055 — confirmation alignement dock
- Intégration Raspberry Pi Zero 2W dans le boîtier
- Pi Camera Module 3 NoIR — vision de base
- Dock MVP : émetteur Qi vertical, alimentation

**Matériel** : Batteries LiPo ×2, BQ25895, bobine Qi + BQ51051B, aimants N35, DRV5055, Pi Zero 2W, Pi Camera 3 NoIR

---

## Phase 5 — Wi-Fi + API REST

**Objectif** : CHIBI est pilotable par réseau.

- Serveur HTTP embarqué avec `picoserve` sur l'ESP32-S3
- `embassy-net` pour la gestion Wi-Fi
- API sémantique : `/emotion/event`, `/notification`, `/display/text`, `/state`
- API primitive : `/display/expression`, `/motion/dance`, `/led/color`, `/sensors`
- WebSocket pour le streaming d'état en temps réel
- Communication UART entre ESP32-S3 et Pi Zero 2W
- Dock complète : Raspberry Pi 5 8GB + SSD NVMe 256GB
- Broker MQTT (Mosquitto) sur le Pi 5
- Serveur OTA sur le Pi 5

**Matériel** : Raspberry Pi 5 8GB, SSD NVMe 256GB, active cooler, boîtier dock

---

## Phase 6 — Écosystème plugins

**Objectif** : CHIBI devient une plateforme extensible.

- Plugin manager sur le Pi 5 (découverte, installation, mise à jour)
- Système de priorités et fusion d'états entre plugins
- Plugins officiels :
  - `github-ci` — réactions aux GitHub Actions
  - `weather` — humeur selon la météo locale
  - `pomodoro` — compagnon de sessions
  - `home-assistant` — intégration domotique
  - `mqtt-bridge` — bridge IoT générique
  - `dev-mood` — connecté à l'IDE
  - `charge-screen` — écran d'informations pendant la charge
- `chibi-cli` — outil en ligne de commande (`chibi install`, `chibi enable`, `chibi status`)
- Registry de plugins (format de manifeste, validation)
- Companion app Tauri + Leptos (configuration, visualisation, logs)

---

## Phase 7 — Mémoire & personnalité évolutive

**Objectif** : CHIBI développe une personnalité unique au fil du temps.

- Persistance en flash (NVS) des paramètres de personnalité
- Historique des interactions (fréquence de caresses, patterns d'utilisation)
- Adaptation progressive des réactions (CHIBI « apprend » les préférences)
- Paramètres de tempérament configurables (timide ↔ extraverti, calme ↔ nerveux)
- Journaux d'humeur consultables via l'app companion

---

## Phase 8 — IA locale

**Objectif** : CHIBI comprend et interagit vocalement.

- Wake word detection sur le Pi Zero 2W
- Vision : détection de visages, reconnaissance de gestes (Pi Camera)
- LLM local via Ollama sur le Pi 5 (conversation basique, personnalité)
- Text-to-speech local pour réponses vocales
- Intégration au moteur émotionnel (le LLM influence l'humeur)

---

## Questions ouvertes

Ces questions seront tranchées au cours du développement :

- **Mécanisme d'éjection dock** — Comment CHIBI se détache de la dock après la charge ? Came actionnée par servo, ressort, électroaimant ? À décider lors de la conception mécanique de la dock.
- **Format exact des logements batterie** — Les dimensions précises des cavités dans les jambes dépendent de la conception mécanique finale. Le format JST-PH 2mm est fixé, mais la géométrie exacte reste à définir.
- **Protocole OTA** — Le mécanisme de mise à jour over-the-air du firmware ESP32-S3 doit être défini. Options : HTTP pull depuis le Pi 5, push via MQTT, ou intégration avec un service dédié.

---

## Architecture Decision Records (ADRs)

Les décisions d'architecture clés sont documentées dans [`docs/adr/`](docs/adr/) :

| ADR | Titre |
|-----|-------|
| [001](docs/adr/001-plugin-api.md) | API Plugin : REST + WebSocket vs MQTT seul |
| [002](docs/adr/002-emotion-engine.md) | Moteur émotionnel : axes continus vs états discrets |
| [003](docs/adr/003-dynamixel-uart.md) | Servos : bus UART Dynamixel 2.0 vs PWM individuel |
| [004](docs/adr/004-battery-legs.md) | Batterie : deux cellules LiPo dans les jambes |
| [005](docs/adr/005-qi-back.md) | Charge Qi : bobine dans le dos vs pieds vs pogo pins |
| [006](docs/adr/006-lilygo-t4s3.md) | Board : LilyGo T4-S3 vs composants séparés |
]]>