# ADR 002 — Moteur émotionnel : axes continus vs états discrets

**Date** : 2025-03-19

**Statut** : Accepté

## Contexte

CHIBI doit exprimer des émotions via son écran AMOLED (yeux, sourcils, bouche) et ses LEDs. Plusieurs plugins peuvent simultanément vouloir influencer l'état émotionnel (le plugin CI/CD après un échec de build, le plugin météo qui reflète la pluie, le plugin pomodoro pendant une session). Il faut un modèle émotionnel qui permette la fusion de ces influences et des transitions fluides entre états.

## Options considérées

### Option A — États discrets (idle, happy, sad, angry, surprised...)

- Enum fini d'états émotionnels
- Chaque plugin demande un état précis
- Le dernier plugin à écrire gagne (ou système de priorité simple)

**Avantages** : simple à implémenter, facile à comprendre pour les développeurs de plugins.

**Inconvénients** : transitions brusques entre états, pas de nuance (un peu triste vs très triste), conflits quand deux plugins veulent des états différents, nombre d'expressions limité par le nombre d'états définis.

### Option B — Axes continus valence + énergie (retenue)

- Deux axes continus : **valence** (triste ↔ heureux, -1.0 à +1.0) et **énergie** (calme ↔ excité, 0.0 à 1.0)
- Les expressions sont **paramétriques** : les yeux, sourcils et bouche sont générés dynamiquement à partir des valeurs des axes (ouverture des yeux, inclinaison des sourcils, courbure de la bouche, position de la pupille)
- Fusion des influences par **layers de plugins** avec priorité (0–100) et durée d'effet
- Interpolation fluide entre les positions sur les axes

**Avantages** : expressions infiniment variées, transitions douces, fusion naturelle des influences multiples, les plugins n'ont pas besoin de connaître les expressions disponibles.

**Inconvénients** : plus complexe à implémenter, mapping axes → paramètres visuels à calibrer, moins intuitif pour les développeurs de plugins débutants.

### Option C — Système de blend shapes (style animation 3D)

- Paramètres individuels pour chaque élément du visage
- Les plugins contrôlent directement les blend shapes

**Écarté** : trop granulaire pour l'API plugin, mieux adapté comme couche interne sous les axes continus.

## Décision

Moteur émotionnel basé sur **deux axes continus** (valence + énergie) avec **fusion par layers de plugins**.

Chaque plugin déclare :
- Une **priorité** (0–100) : détermine quel plugin a le dernier mot en cas de conflit
- Une **durée d'effet** : combien de temps l'influence persiste

Le firmware maintient une pile d'influences actives, triées par priorité. L'état émotionnel résultant est la combinaison pondérée des influences actives. Quand une influence expire, le système revient fluidement à l'influence de priorité inférieure.

Les expressions paramétriques sont générées via `embedded-graphics` : pas de sprites statiques, mais des formes géométriques (arcs, ellipses, bézier) dont les paramètres dépendent des valeurs des axes.

## Conséquences

### Positives

- Expressions naturelles et uniques — pas de « sprite sheet » limitée
- Transitions fluides entre émotions (interpolation sur les axes)
- Un plugin CI/CD (priorité 80) peut influencer l'humeur pendant 30s sans écraser définitivement le plugin météo (priorité 20)
- Extensible : on peut ajouter un troisième axe (confiance, curiosité) sans casser l'existant
- Les paramètres visuels (ouverture yeux, sourcils) sont calculés, pas dessinés → très léger en mémoire

### Négatives

- Le mapping (valence, énergie) → paramètres visuels demande du calibrage et du playtesting
- L'API sémantique (`/emotion/event { type: "success" }`) doit traduire les événements en valeurs d'axes, ce qui ajoute une couche d'abstraction
- Debug moins visuel qu'un système à états nommés (il faut visualiser les axes)
