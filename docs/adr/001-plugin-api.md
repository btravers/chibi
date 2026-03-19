<![CDATA[# ADR 001 — API Plugin : REST + WebSocket vs MQTT seul

**Date** : 2025-03-19

**Statut** : Accepté

## Contexte

CHIBI est conçu comme une plateforme extensible via plugins. Un plugin est un processus autonome, écrit dans n'importe quel langage, qui pilote le comportement du robot (émotions, mouvements, affichage). Il faut choisir le protocole de communication entre les plugins et le firmware embarqué sur l'ESP32-S3.

Le système doit être accessible au plus grand nombre de développeurs, fonctionner en réseau local, et supporter à la fois des commandes ponctuelles (déclencher une émotion) et du streaming continu (état des capteurs en temps réel).

## Options considérées

### Option A — MQTT seul

- Broker Mosquitto sur le Pi 5 de la dock
- Les plugins publient sur des topics (`chibi/emotion/event`, `chibi/motion/dance`)
- Le firmware souscrit aux topics pertinents
- Streaming d'état via topics de publication (`chibi/state`, `chibi/sensors`)

**Avantages** : protocole léger, bien adapté à l'IoT, pub/sub natif, découplage naturel.

**Inconvénients** : nécessite un broker (dépendance à la dock), MQTT est moins familier que HTTP pour la majorité des développeurs, pas de sémantique requête/réponse native, debug plus complexe (pas de `curl`).

### Option B — REST + WebSocket (retenue)

- Serveur HTTP embarqué directement sur l'ESP32-S3 via `picoserve`
- Les plugins envoient des requêtes HTTP standard (`POST /emotion/event`, `GET /state`)
- WebSocket pour le streaming d'état en temps réel
- MQTT conservé uniquement pour l'intégration Home Assistant

**Avantages** : HTTP est universel (tout langage a un client HTTP), testable avec `curl`, sémantique requête/réponse claire, pas de dépendance à un broker, fonctionne même sans dock.

**Inconvénients** : un peu plus lourd que MQTT sur l'ESP32, WebSocket à maintenir côté firmware.

### Option C — gRPC

- Communication via Protocol Buffers et gRPC

**Écarté** : trop lourd pour un microcontrôleur, écosystème embedded quasi inexistant.

## Décision

**REST + WebSocket** comme protocole principal de l'API plugin, avec `picoserve` comme serveur HTTP embarqué sur l'ESP32-S3. MQTT reste utilisé pour la communication avec Home Assistant via le plugin `mqtt-bridge`, mais n'est pas le protocole principal.

Deux niveaux d'API sont définis :
- **API sémantique** (haut niveau) : événements métier (`/emotion/event`, `/notification`)
- **API primitive** (bas niveau) : contrôle direct des actuateurs (`/display/expression`, `/motion/dance`)

## Conséquences

### Positives

- Tout développeur peut écrire un plugin avec un simple client HTTP
- Debug facile avec `curl` ou Postman
- CHIBI fonctionne en mode autonome (sans dock) — le serveur HTTP est embarqué
- `picoserve` est inspiré d'Axum, API familière pour les développeurs Rust

### Négatives

- Le serveur HTTP sur ESP32-S3 consomme plus de ressources qu'un client MQTT
- WebSocket ajoute de la complexité au firmware
- Les plugins doivent connaître l'adresse IP de CHIBI (résolu par mDNS)
]]>