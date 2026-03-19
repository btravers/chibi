<![CDATA[# ADR 006 — Board : LilyGo T4-S3 vs composants séparés

**Date** : 2025-03-19

**Statut** : Accepté

## Contexte

L'écran cible pour les expressions de CHIBI est le RM690B0, un AMOLED 2.41" de résolution 600×450 pixels avec 16.7M de couleurs. Ses noirs parfaits sont essentiels : le fond noir est « gratuit » (pixels éteints = zéro consommation), ce qui fait ressortir les yeux de manière spectaculaire.

Il faut choisir entre utiliser une board intégrée qui combine le MCU et l'écran, ou assembler les composants séparément (ESP32-S3 DevKit + écran connecté manuellement).

## Options considérées

### Option A — Composants séparés (ESP32-S3 DevKit + écran)

- ESP32-S3-DevKitC-1 (~8€) + écran RM690B0 nu (~25€)
- Câblage QSPI manuel entre la board et l'écran
- Possibilité d'utiliser un autre écran (GC9A01 rond, ST7789, etc.)

**Avantages** : flexibilité du choix d'écran, composants individuellement remplaçables, potentiellement moins cher.

**Inconvénients** : câblage QSPI complexe (6+ fils haute fréquence), pas de garantie de fonctionnement du driver Rust sans la board de référence, pas d'IMU intégré, plus de place occupée dans le boîtier.

### Option B — LilyGo T4-S3 (retenue)

- Board intégrée : ESP32-S3R8 + RM690B0 AMOLED 2.41" + interface QSPI
- QMI8658 IMU intégré sur la board
- 8MB PSRAM pour le framebuffer
- Schémas et pinout documentés par LilyGo

**Avantages** : tout est câblé et testé, le driver `rm690b0-rs` cible spécifiquement cette board, framebuffer PSRAM + DMA déjà configuré dans les exemples, IMU inclus gratuitement.

**Inconvénients** : dépendance à un fournisseur unique (LilyGo), moins de flexibilité si l'on veut changer d'écran.

### Option C — Autre board intégrée (ESP32-S3 + GC9A01)

- Boards avec écran rond GC9A01 (ex: Waveshare ESP32-S3 1.28")

**Écarté** : le GC9A01 est un écran rond de 240×240 pixels, trop petit et trop basse résolution pour afficher deux yeux expressifs côte à côte. Le format rectangulaire du RM690B0 (600×450) est nécessaire pour le design visé (deux yeux sur un seul écran, style EMO).

## Décision

**LilyGo T4-S3** comme board principale du firmware CHIBI.

Raisons déterminantes :
1. Le driver Rust `rm690b0-rs` (maintenu par The Embedded Rustacean) **cible spécifiquement cette board** — les exemples, la configuration PSRAM/DMA, et les pinouts sont documentés pour le T4-S3
2. L'écran RM690B0 AMOLED est **déjà connecté en QSPI** — pas de câblage haute fréquence à faire
3. Le **QMI8658 IMU est intégré** — un capteur de moins à câbler et à loger dans le boîtier
4. Le **framebuffer en PSRAM** (8MB) avec transferts DMA est déjà configuré dans les exemples du driver
5. Les **schémas sont disponibles** — pas de reverse engineering nécessaire

## Conséquences

### Positives

- **~2€ d'économie** par rapport aux composants séparés (T4-S3 ~35€ vs DevKit ~8€ + écran nu ~25€ + connecteurs ~4€)
- **Moins de câblage** : écran QSPI et IMU déjà connectés, gain de place dans le boîtier
- **Migration directe** vers `rm690b0-rs` : le driver est testé et maintenu pour cette board exacte
- **IMU gratuit** : le QMI8658 intégré fournit accéléromètre et gyroscope sans composant supplémentaire
- **Fiabilité** : connexion QSPI haute fréquence faite en usine, pas de risque de mauvais contact

### Négatives

- **Dépendance fournisseur** : si LilyGo arrête la production du T4-S3, il faudra migrer vers une autre solution
- **Flexibilité réduite** : changer d'écran nécessiterait de changer de board
- **Form factor** : la taille et la forme du T4-S3 contraignent la conception du boîtier
]]>