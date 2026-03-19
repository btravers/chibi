<![CDATA[# CHIBI — Bill of Materials (BOM)

Liste complète du matériel nécessaire, organisée par catégorie. Les prix sont indicatifs (mars 2025) et peuvent varier selon les fournisseurs et la disponibilité.

---

## Cerveaux CHIBI (mobile)

| Composant | Modèle | Rôle | Fournisseur | Phase | Prix |
|-----------|--------|------|-------------|-------|------|
| MCU + écran | LilyGo T4-S3 (ESP32-S3R8 + RM690B0 AMOLED 2.41") | Cerveau temps réel, affichage, capteurs, servos | AliExpress / LilyGo | 1 | ~35 € |
| SBC | Raspberry Pi Zero 2 W | Orchestration, vision, bridge | RPi Foundation | 4 | ~22 € |
| Caméra | Pi Camera Module 3 NoIR | Vision, détection visages/gestes | RPi Foundation | 4 | ~30 € |
| Câble caméra | Câble FPC Pi Zero → Camera | Connexion caméra | RPi Foundation | 4 | ~4 € |

## Cerveaux Dock

| Composant | Modèle | Rôle | Fournisseur | Phase | Prix |
|-----------|--------|------|-------------|-------|------|
| SBC | Raspberry Pi 5 8GB | Plugin manager, MQTT, Ollama, OTA | RPi Foundation | 5 | ~95 € |
| Stockage | SSD NVMe 256GB (M.2 2230) | Stockage dock (plugins, modèles IA) | Amazon | 5 | ~35 € |
| HAT NVMe | Pi 5 NVMe HAT | Adaptateur M.2 pour Pi 5 | Pimoroni / Geekworm | 5 | ~15 € |
| Refroidissement | Active Cooler Pi 5 | Refroidissement actif | RPi Foundation | 5 | ~8 € |
| Alimentation dock | USB-C 5V 5A (27W) | Alimentation Pi 5 + émetteur Qi | RPi Foundation | 5 | ~15 € |

## Capteurs

| Composant | Modèle | Rôle | Fournisseur | Phase | Prix |
|-----------|--------|------|-------------|-------|------|
| Tactile capacitif | MPR121 breakout (12 canaux) | Détection toucher tête | Adafruit | 1 | ~8 € |
| IMU | QMI8658 (intégré T4-S3) | Orientation, chute, secousses | — (intégré) | 1 | — |
| ToF laser | VL53L1X | Détection obstacles (portée 4m) | Pololu / AliExpress | 2 | ~12 € |
| ToF anti-chute | VL6180X ×4 | Anti-chute, 4 coins sous les pieds | Adafruit / AliExpress | 2 | ~28 € (4×7) |
| Lumière ambiante | VEML7700 | Adaptation luminosité | Adafruit / AliExpress | 2 | ~5 € |
| Micro MEMS | ICS-43434 (I2S) | Réaction aux sons | Adafruit / AliExpress | 2 | ~7 € |
| Temp/humidité | SHT40 Sensirion | Environnement ambiant | Adafruit / Mouser | 2 | ~6 € |
| Capteur Hall | DRV5055 | Détection alignement dock | Mouser / DigiKey | 4 | ~3 € |

## Locomotion

| Composant | Modèle | Rôle | Fournisseur | Phase | Prix |
|-----------|--------|------|-------------|-------|------|
| Servos validation | SG90 ×4 | Validation cinématique (jetables) | AliExpress | 3a | ~8 € (4×2) |
| Servos hanches+chevilles | Dynamixel XL330-M077-T ×4 | Locomotion bipède définitive | Robotis | 3b | ~100 € (4×25) |
| Servo tête | Dynamixel XL330-M288-T ×1 | Mouvement de tête | Robotis | 3b | ~25 € |
| Câble Dynamixel | Câble JST 3 broches ×5 | Bus daisychain UART | Robotis | 3b | ~10 € |

## Actionneurs corps

| Composant | Modèle | Rôle | Fournisseur | Phase | Prix |
|-----------|--------|------|-------------|-------|------|
| LEDs | SK6812 RGBW (strip ou ring) | Rétroéclairage expressif | AliExpress | 2 | ~5 € |

## Audio

| Composant | Modèle | Rôle | Fournisseur | Phase | Prix |
|-----------|--------|------|-------------|-------|------|
| Haut-parleur | Mini speaker 8Ω 1W (20mm) | Sortie audio (bips, sons) | AliExpress | 2 | ~3 € |
| Ampli | MAX98357A (I2S) | Amplificateur audio | Adafruit / AliExpress | 2 | ~5 € |

## Alimentation

| Composant | Modèle | Rôle | Fournisseur | Phase | Prix |
|-----------|--------|------|-------------|-------|------|
| Batterie | LiPo 1800mAh 3.7V JST-PH ×2 | Alimentation mobile (3600mAh total) | AliExpress / Amazon | 4 | ~16 € (2×8) |
| Gestion charge | BQ25895 breakout | Charge LiPo, monitoring I2C | Adafruit / AliExpress | 4 | ~8 € |
| Régulateur | LDO 3.3V (AMS1117 ou equiv.) | Alimentation capteurs | AliExpress | 1 | ~2 € |

> **Note** : Les batteries LiPo sont au format standard JST-PH 2mm pour permettre un upgrade futur vers des cellules Si-C (silicium-carbone) sans modification mécanique des logements.

## Charge Qi

| Composant | Modèle | Rôle | Fournisseur | Phase | Prix |
|-----------|--------|------|-------------|-------|------|
| Récepteur Qi | Module Qi 10W + BQ51051B (avec ferrite) | Réception charge sans fil | AliExpress | 4 | ~8 € |
| Émetteur Qi | Module émetteur Qi 10W | Émission charge (dock) | AliExpress | 4 | ~10 € |
| Aimants | N35 néodyme 6×2mm ×8 | Alignement MagSafe | AliExpress | 4 | ~4 € |

## Connectique

| Composant | Modèle | Rôle | Fournisseur | Phase | Prix |
|-----------|--------|------|-------------|-------|------|
| Connecteurs I2C | JST-SH 4 broches (Qwiic/Stemma QT) | Bus I2C capteurs | Adafruit / AliExpress | 1 | ~5 € |
| PCB prototype | Perfboard / PCB custom | Interconnexion composants | AliExpress | 1 | ~5 € |
| Fils | Fils silicone AWG 26-30 (assortiment) | Câblage interne | AliExpress | 1 | ~5 € |

## Électronique / Outillage

| Composant | Modèle | Rôle | Fournisseur | Phase | Prix |
|-----------|--------|------|-------------|-------|------|
| Level shifter | TXB0104 (bidirectionnel) | Conversion niveaux logiques 3.3V ↔ 5V | Adafruit / AliExpress | 2 | ~3 € |
| Multimètre | (tout modèle basique) | Debug électronique | Amazon | 1 | ~15 € |

## Mécanique / Impression 3D

| Composant | Modèle | Rôle | Fournisseur | Phase | Prix |
|-----------|--------|------|-------------|-------|------|
| Filament PLA | PLA 1.75mm (1kg, couleur au choix) | Impression du boîtier | Amazon / AliExpress | 3 | ~20 € |
| Vis / inserts | Kit vis M2/M3 + inserts filetés thermiques | Assemblage boîtier | AliExpress | 3 | ~8 € |
| Pieds caoutchouc | Patins silicone adhésifs | Adhérence au sol | AliExpress | 3 | ~3 € |

---

## Niveaux de budget

### Phase 1 seule — ~192 €

Premier signe de vie : écran avec yeux animés et réaction au toucher.

| Poste | Détail | Coût |
|-------|--------|------|
| LilyGo T4-S3 | MCU + écran AMOLED | 35 € |
| MPR121 | Capteur tactile | 8 € |
| Régulateur 3.3V | Alimentation capteurs | 2 € |
| Connectique | JST, PCB, fils | 15 € |
| Servos SG90 ×4 | Validation locomotion (Phase 3a) | 8 € |
| Filament PLA | Impression châssis Otto | 20 € |
| Vis / inserts | Assemblage | 8 € |
| Capteurs Phase 2 | VL53L1X, VL6180X ×4, VEML7700, ICS-43434, SHT40 | 58 € |
| LEDs + audio | SK6812, speaker, ampli | 13 € |
| Level shifter | TXB0104 | 3 € |
| Pieds caoutchouc | Adhérence | 3 € |
| Multimètre | Debug | 15 € |
| Divers | Marge composants | 4 € |
| **Total** | | **~192 €** |

### CHIBI complet (mobile) — ~492 €

Robot bipède autonome avec charge Qi, tous les capteurs, servos Dynamixel.

| Poste | Détail | Coût |
|-------|--------|------|
| Phase 1 (ci-dessus) | | 192 € |
| Dynamixel XL330-M077-T ×4 | Servos locomotion | 100 € |
| Dynamixel XL330-M288-T | Servo tête | 25 € |
| Câbles Dynamixel | Bus daisychain | 10 € |
| Pi Zero 2W + câble caméra | Second cerveau | 26 € |
| Pi Camera 3 NoIR | Vision | 30 € |
| Batteries LiPo ×2 | 3600mAh total | 16 € |
| BQ25895 | Gestion charge | 8 € |
| Module Qi récepteur | Charge sans fil | 8 € |
| Aimants N35 ×8 | Alignement dock | 4 € |
| DRV5055 | Capteur Hall | 3 € |
| Boîtier custom | Filament supplémentaire | 10 € |
| Divers | Marge intégration | 60 € |
| **Total** | | **~492 €** |

### Projet entier avec dock — ~841 €

Installation complète : CHIBI mobile + dock station avec Pi 5.

| Poste | Détail | Coût |
|-------|--------|------|
| CHIBI complet (ci-dessus) | | 492 € |
| Raspberry Pi 5 8GB | Cerveau dock | 95 € |
| SSD NVMe 256GB | Stockage | 35 € |
| HAT NVMe | Adaptateur M.2 | 15 € |
| Active Cooler Pi 5 | Refroidissement | 8 € |
| Alimentation 27W | USB-C Pi 5 | 15 € |
| Émetteur Qi 10W | Charge dock | 10 € |
| Boîtier dock | Impression 3D + visserie | 15 € |
| Divers dock | Câblage, marge | 156 € |
| **Total** | | **~841 €** |
]]>