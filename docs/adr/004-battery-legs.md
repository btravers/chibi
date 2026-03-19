<![CDATA[# ADR 004 — Batterie : deux cellules LiPo dans les jambes

**Date** : 2025-03-19

**Statut** : Accepté

## Contexte

CHIBI est un robot bipède autonome sur batterie. Le passage d'une architecture sur roues à une architecture bipède (Otto DIY) a changé les contraintes d'alimentation et d'équilibre. Un robot bipède est intrinsèquement moins stable qu'un robot à roues — le centre de gravité doit être le plus bas possible pour faciliter la marche.

La capacité cible est d'environ 3600mAh (3.7V) pour permettre plusieurs heures d'autonomie avec l'écran AMOLED, les capteurs, le Wi-Fi et les servos.

## Options considérées

### Option A — Une seule batterie dans le corps

- Un pack LiPo unique (3600mAh) logé dans le torse
- Centre de gravité en haut du robot

**Avantages** : simple à intégrer, un seul connecteur, monitoring trivial.

**Inconvénients** : centre de gravité haut (instabilité bipède), encombrement du torse (espace déjà occupé par les boards et capteurs), pack custom nécessaire.

### Option B — Deux cellules dans les jambes (retenue)

- 2× LiPo 1800mAh 3.7V (format standard JST-PH 2mm) dans des logements dédiés dans les jambes
- Cellules connectées en parallèle (3600mAh total, 3.7V)
- BQ25895 pour la gestion de charge et le monitoring I2C

**Avantages** : centre de gravité très bas (stabilité), répartition symétrique de la masse, format standard (upgrade futur facile), libère l'espace du torse.

**Inconvénients** : deux connecteurs à câbler, logements à concevoir dans les jambes, le parallèle nécessite des cellules appairées.

### Option C — Batterie dans les pieds

- Cellules plates dans la semelle des pieds

**Écarté** : les pieds sont les pièces les plus mobiles et les plus sollicitées mécaniquement. Le câblage serait complexe et fragile au niveau des articulations.

## Décision

**Deux cellules LiPo 1800mAh 3.7V au format standard JST-PH 2mm**, logées dans les jambes de CHIBI, connectées en parallèle pour fournir 3600mAh total.

Le BQ25895 gère la charge des deux cellules en parallèle et monitore leur état (tension, courant, température) via I2C.

Les logements dans les jambes sont dimensionnés pour accueillir des cellules au format standard, ce qui permettra à terme un **upgrade vers des cellules Si-C** (silicium-carbone, densité énergétique supérieure) de même format sans modification mécanique.

## Conséquences

### Positives

- **Centre de gravité bas** : les batteries dans les jambes lestent le bas du robot, améliorant significativement la stabilité bipède
- **Répartition symétrique** : masse égale dans chaque jambe, pas de déséquilibre latéral
- **Format standard** : les cellules JST-PH 2mm sont largement disponibles et interchangeables
- **Upgrade futur** : le passage aux cellules Si-C (plus denses) ne nécessitera aucune modification mécanique
- **Espace torse libéré** : plus de place pour les boards, capteurs et câblage

### Négatives

- **Câblage** : deux connecteurs à router depuis les jambes vers le BQ25895 dans le torse, à travers les articulations
- **Appairage** : les deux cellules en parallèle doivent idéalement être du même lot pour un comportement homogène
- **Conception mécanique** : les logements dans les jambes ajoutent de la complexité à la conception 3D (dimensions exactes à déterminer)
]]>