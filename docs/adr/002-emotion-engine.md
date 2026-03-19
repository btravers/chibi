# ADR 002 — Emotion Engine: Continuous Axes vs Discrete States

**Date**: 2025-03-19

**Status**: Accepted

## Context

CHIBI must express emotions through its AMOLED screen (eyes, eyebrows, mouth) and LEDs. Multiple plugins may simultaneously want to influence the emotional state (the CI/CD plugin after a build failure, the weather plugin reflecting rain, the pomodoro plugin during a session). We need an emotional model that allows merging these influences and smooth transitions between states.

## Options Considered

### Option A — Discrete States (idle, happy, sad, angry, surprised...)

- Finite enum of emotional states
- Each plugin requests a specific state
- Last plugin to write wins (or simple priority system)

**Pros**: simple to implement, easy for plugin developers to understand.

**Cons**: abrupt transitions between states, no nuance (slightly sad vs very sad), conflicts when two plugins want different states, number of expressions limited by the number of defined states.

### Option B — Continuous Valence + Arousal Axes (selected)

- Two continuous axes: **valence** (sad ↔ happy, -1.0 to +1.0) and **arousal** (calm ↔ excited, 0.0 to 1.0)
- Expressions are **parametric**: eyes, eyebrows and mouth are dynamically generated from axis values (eye aperture, eyebrow tilt, mouth curvature, pupil position)
- Influence merging via **plugin layers** with priority (0–100) and effect duration
- Smooth interpolation between positions on the axes

**Pros**: infinitely varied expressions, smooth transitions, natural merging of multiple influences, plugins don't need to know the available expressions.

**Cons**: more complex to implement, axis → visual parameter mapping needs calibration, less intuitive for beginner plugin developers.

### Option C — Blend Shapes System (3D animation style)

- Individual parameters for each facial element
- Plugins directly control the blend shapes

**Rejected**: too granular for the plugin API, better suited as an internal layer beneath the continuous axes.

## Decision

Emotion engine based on **two continuous axes** (valence + arousal) with **plugin layer merging**.

Each plugin declares:
- A **priority** (0–100): determines which plugin has the final say in case of conflict
- An **effect duration**: how long the influence persists

The firmware maintains a stack of active influences, sorted by priority. The resulting emotional state is the weighted combination of active influences. When an influence expires, the system smoothly falls back to the next lower-priority influence.

Parametric expressions are generated via `embedded-graphics`: no static sprites, but geometric shapes (arcs, ellipses, bezier curves) whose parameters depend on the axis values.

## Consequences

### Positive

- Natural and unique expressions — no limited sprite sheet
- Smooth transitions between emotions (interpolation on the axes)
- A CI/CD plugin (priority 80) can influence the mood for 30s without permanently overriding the weather plugin (priority 20)
- Extensible: a third axis (confidence, curiosity) can be added without breaking existing functionality
- Visual parameters (eye aperture, eyebrows) are calculated, not drawn — very lightweight in memory

### Negative

- The (valence, arousal) → visual parameter mapping requires calibration and playtesting
- The semantic API (`/emotion/event { type: "success" }`) must translate events into axis values, adding an abstraction layer
- Less visually debuggable than a named-state system (you need to visualize the axes)
