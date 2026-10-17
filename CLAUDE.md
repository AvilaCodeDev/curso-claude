# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the Game

No build step required. Two options:
- Open `index.html` directly in a browser
- `npx serve .` → visit `http://localhost:3000`

No package.json, no bundler, no test framework, no linter.

## Architecture

Single-file game: `index.html` loads `game.js` (all logic, ~424 lines).

**Entity classes** (each has `update(dt)` and `draw()` methods):
- `Ship` — player, handles thrust/rotation/invincibility
- `Asteroid` — irregular polygon, 3 size tiers, splits on death
- `Bullet` — projectile with TTL
- `Particle` — explosion debris

**Global game state** (declared around line 238): `ship`, `bullets`, `asteroids`, `particles`, `score`, `lives`, `level`, `state`.

**Game loop** (`requestAnimationFrame`): `update(dt)` → `draw()` → repeat. Delta time is clamped to 50ms.

**Input**: `keys[]` tracks held keys; `justPressed[]` tracks single-press (consumed by `pressed()`).

**Collision**: Circle-based distance checks — bullet↔asteroid and ship↔asteroid only.

**Dead-entity cleanup**: entities set `this.dead = true`; arrays are filtered each frame.

**Toroidal wrapping**: `wrap(v, max)` applied to all moving entities so they reappear on the opposite edge.

## Key Details

- UI text and comments are in Spanish (course project)
- Scoring: large asteroid = 20pts, medium = 50pts, small = 100pts
- Ship spawns with 3s invincibility (blinking visual)
- Game states: `'playing'` → `'dead'` (2s wait) → respawn or `'gameover'`
- Draw order: particles → asteroids → bullets → ship → HUD
