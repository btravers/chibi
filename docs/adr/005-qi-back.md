# ADR 005 — Charge Qi : bobine dans le dos vs pieds vs pogo pins

**Date** : 2025-03-19

**Statut** : Accepté

## Contexte

CHIBI doit pouvoir se recharger de manière autonome en revenant sur sa dock station. Étant un robot bipède, les pieds sont en mouvement constant pendant la marche, ce qui rend le placement de la bobine de charge critique. Le mécanisme doit être fiable, reproductible, et ne pas nécessiter d'intervention humaine pour aligner CHIBI correctement.

## Options considérées

### Option A — Bobine Qi dans les pieds

- Bobine réceptrice sous les semelles
- Émetteur horizontal dans la dock (comme un chargeur de téléphone classique)

**Avantages** : concept intuitif (CHIBI se tient « sur » le chargeur).

**Inconvénients** : les pieds bougent constamment pendant la marche, l'alignement doit être précis à chaque pas, les articulations des chevilles compliquent le câblage, l'usure mécanique est maximale sur cette zone.

### Option B — Pogo pins (contacts physiques)

- Contacts pogo pins dans les pieds ou le dos
- Contacts correspondants sur la dock

**Avantages** : transfert d'énergie efficace, pas de pertes inductives.

**Inconvénients** : alignement mécanique très précis nécessaire, usure des contacts, oxydation possible, complexité mécanique du mécanisme d'insertion.

### Option C — Bobine Qi dans le dos (retenue)

- Bobine réceptrice Qi 10W (BQ51051B avec ferrite intégrée) dans le dos de CHIBI
- Émetteur Qi vertical dans la dock
- CHIBI recule pour s'aligner avec la dock
- Aimants N35 6×2mm en anneau (style MagSafe) pour l'alignement
- Capteur Hall DRV5055 pour confirmer l'alignement

**Avantages** : surface de contact fixe et prévisible (le dos ne bouge pas pendant la marche), alignement magnétique fiable, pas d'usure mécanique.

**Inconvénients** : CHIBI doit se retourner pour se charger (recule vers la dock), mécanisme d'éjection à concevoir.

## Décision

**Bobine réceptrice Qi 10W dans le dos de CHIBI**, avec émetteur vertical dans la dock et alignement magnétique style MagSafe.

Le processus de charge :
1. CHIBI détecte un niveau de batterie bas
2. CHIBI se dirige vers la dock et se retourne
3. CHIBI recule jusqu'à ce que les aimants s'engagent
4. Le capteur Hall DRV5055 confirme l'alignement
5. La charge Qi démarre
6. L'écran passe en mode charge (plugin `charge-screen`)

La **question du mécanisme d'éjection** (comment CHIBI se détache de la dock après la charge) est **intentionnellement laissée ouverte**. Les options envisagées incluent une came actionnée par servo, un ressort, ou un électroaimant inversé. Cette décision sera prise lors de la conception mécanique de la dock.

## Conséquences

### Positives

- **Surface fixe** : le dos est la partie la plus stable du robot, pas de mouvement pendant la marche
- **Alignement fiable** : les aimants N35 en anneau assurent un centrage précis et reproductible
- **Confirmation électronique** : le capteur Hall DRV5055 permet au firmware de vérifier l'alignement avant de tenter la charge
- **Sans contact** : pas d'usure mécanique, pas d'oxydation, pas de poussière sur les contacts
- **Standard Qi** : technologie mature et bien documentée

### Négatives

- **Éjection** : le mécanisme pour détacher CHIBI de la dock reste à concevoir (question ouverte)
- **Orientation** : CHIBI doit se retourner (marche arrière) pour se charger, ce qui demande une cinématique de demi-tour fiable
- **Pertes inductives** : la charge Qi est moins efficace que des contacts directs (~80% vs ~95%)
- **Aimants** : les aimants N35 pourraient interférer avec la boussole de l'IMU (à vérifier)
