<![CDATA[# ADR 003 — Servos : bus UART Dynamixel 2.0 vs PWM individuel

**Date** : 2025-03-19

**Statut** : Accepté

## Contexte

CHIBI est un robot bipède avec 4 servos de locomotion (2 hanches + 2 chevilles) et 1 servo pour la tête, soit 5 servos au total. Le choix du protocole de communication avec les servos impacte le câblage, les capacités de retour d'information (télémétrie), et la qualité du contrôle de mouvement.

L'architecture mécanique est basée sur Otto DIY, un robot bipède open source dont les STL sont disponibles sur Printables.

## Options considérées

### Option A — PWM individuel avec servos SG90/MG90S

- 5 fils PWM individuels depuis l'ESP32-S3 vers chaque servo
- Servos analogiques bon marché (SG90 ~2€, MG90S ~5€)
- Contrôle en boucle ouverte (pas de retour de position)

**Avantages** : très bon marché, simple à programmer, abondance de librairies et exemples.

**Inconvénients** : 5 GPIOs occupés, pas de retour de position ni de couple (boucle ouverte), câblage en étoile complexe dans un petit boîtier, impossible de détecter un blocage mécanique, usure non détectable.

### Option B — Bus UART Dynamixel 2.0 (retenue)

- 5 servos Dynamixel XL330 connectés en daisychain sur un seul bus UART
- Protocole Dynamixel 2.0 (half-duplex UART, adressage par ID)
- Télémétrie complète : position exacte, vitesse, couple, température, tension

**Avantages** : un seul câble pour tous les servos (daisychain), retour de position et couple en temps réel, détection de blocage et surchauffe, contrôle précis en boucle fermée.

**Inconvénients** : servos plus chers (~25€ l'unité), protocole Dynamixel 2.0 à implémenter en Rust (pas de crate existant), half-duplex UART nécessite une gestion du sens de communication.

### Option C — I2C avec servos PCA9685

- Driver PWM PCA9685 sur bus I2C, contrôlant des servos classiques
- Un seul bus I2C pour le driver, mais servos toujours en boucle ouverte

**Écarté** : résout le problème du nombre de GPIOs mais pas celui du retour de position. Ajoute un composant (PCA9685) sans apporter la télémétrie.

## Décision

**Dynamixel XL330 sur bus UART daisychain** avec protocole Dynamixel 2.0.

- 4× XL330-M077-T (1.0 Nm) pour les hanches et chevilles
- 1× XL330-M288-T (0.34 Nm) pour la tête (couple plus faible suffisant)
- Implémentation du protocole Dynamixel 2.0 en Rust comme **contribution open source**

La validation initiale de la cinématique (Phase 3a) se fait avec des SG90 bon marché sur le châssis Otto DIY, avant la migration vers les Dynamixel (Phase 3b).

## Conséquences

### Positives

- **Câblage simplifié** : un seul câble UART traverse le corps en daisychain au lieu de 5 fils individuels
- **Télémétrie** : température, couple et position exacte accessibles en temps réel
- **Détection de problèmes** : blocage mécanique, surchauffe, usure détectables par le firmware
- **Contrôle précis** : boucle fermée avec retour de position, mouvements plus fluides
- **Contribution open source** : le driver Dynamixel 2.0 en Rust bénéficiera à toute la communauté Rust embedded

### Négatives

- **Coût** : 5 servos Dynamixel = ~135€ vs ~10€ pour 5 SG90
- **Développement** : le protocole Dynamixel 2.0 doit être implémenté from scratch en Rust (pas de crate existant)
- **Half-duplex** : la gestion du sens de communication UART ajoute de la complexité au driver
- **Approvisionnement** : les XL330 sont parfois en rupture de stock chez Robotis
]]>