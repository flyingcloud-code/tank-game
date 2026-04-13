# 3D Tank Game — Design Spec

**Date:** 2026-04-13
**Type:** Browser-based 3D arcade game
**Stack:** Single HTML file, Three.js CDN, vanilla JS, ECS-lite architecture

---

## 1. Vision

A military-realistic 3D tank game with isometric camera, wave survival gameplay, and destructible terrain. Browser-native — just open an HTML file and play.

## 2. Core Gameplay

- **Mode:** Wave survival — enemies spawn in waves of increasing difficulty
- **Controls:** WASD to move, mouse to aim turret, left-click to fire
- **Objective:** Survive as many waves as possible, rack up score
- **Death:** One life per run. Game over screen with score and wave reached.

### Wave Progression

- Wave 1: 3 slow enemies, basic AI
- Each wave: +2 enemies, enemies get slightly faster and tougher
- Brief 5-second pause between waves with "Wave N" overlay
- Difficulty scales enemy speed (+5%), health (+10%), and fire rate

### Scoring

- Kill enemy tank: 100 points
- Wave completion bonus: wave_number × 50
- Destroyed terrain doesn't score

## 3. Visual Style

- **Military realistic:** olive drab tanks, brown/green terrain, metallic shaders
- **Isometric camera:** 45° angle, slightly behind player, looking down
- **Lighting:** Directional sunlight + ambient fill, soft shadows
- **Effects:** Muzzle flash, explosion particles, dust trails on movement, smoke on damage

## 4. Architecture

### ECS-Lite Pattern

```
Entity = integer ID
Components = Map<entityId, ComponentData>
Systems = functions that iterate entities with required components
```

### Components

| Component | Fields | Used By |
|-----------|--------|---------|
| `MeshComponent` | three.js Mesh, type identifier | RenderSystem |
| `Transform` | position (Vector3), rotation (Euler), velocity | MovementSystem, CameraSystem |
| `Health` | current, max | CombatSystem |
| `TankControl` | speed, turnRate, turretAngle, reloadTime, lastFired | PlayerSystem, AISystem |
| `Bullet` | direction, speed, damage, owner | CombatSystem |
| `Destructible` | type (wall/barrel) | CombatSystem |
| `EnemyAI` | state (patrol/chase/flee), targetId, detectionRange | AISystem |

### Systems (execution order)

1. **InputSystem** — reads keyboard/mouse, updates player TankControl intent
2. **MovementSystem** — applies velocity, handles collision with walls/tanks
3. **CameraSystem** — follows player, smooth lerp, clamps to map bounds
4. **AISystem** — enemy targeting, simple chase behavior, firing decisions
5. **CombatSystem** — bullet movement, hit detection, damage, destruction
6. **WaveSystem** — tracks kills, spawns next wave, intermission timer
7. **RenderSystem** — Three.js render pass
8. **ParticleUpdateSystem** — updates explosion/dust particles, removes expired

## 5. Map

- **Size:** 60×60 unit grid
- **Ground:** Flat textured plane (sand/dirt military base)
- **Walls:** Brick walls (destructible, 3 hits), arranged in strategic patterns
- **Barrels:** Explosive barrels (destructible, 1 hit, area damage)
- **Static structures:** Concrete barriers (indestructible), perimeter walls
- **Map layout:** Generated from a simple 2D grid template, extruded to 3D
- **Perimeter:** Indestructible walls enclosing the battlefield

## 6. Player Tank

- **Body:** Olive green box geometry with turret on top
- **Turret:** Rotates independently toward mouse cursor
- **Barrel:** Cylinder extending from turret, fires bullets from tip
- **Movement:** WASD moves the body, turret auto-aims at mouse
- **Fire rate:** 1 shot per 0.5 seconds
- **Health:** 100 HP
- **Bullet:** Small sphere, travels at 40 units/sec, deals 25 damage
- **Speed:** 8 units/sec

## 7. Enemy Tanks

- **Appearance:** Darker green/brown variants, slightly smaller
- **AI behavior:**
  - Patrol randomly until player within detection range (30 units)
  - Chase player when detected
  - Fire at player when in range (25 units) and facing target
  - Simple obstacle avoidance (steer away from walls)
- **Health:** 50 HP (wave 1), scales +10% per wave
- **Fire rate:** 1 shot per 1.5 seconds
- **Speed:** 5 units/sec, scales +5% per wave
- **Bullet damage:** 15

## 8. Destructible Terrain

- **Brick walls:** 3 HP, visually crack/change on damage, crumble on destruction
- **Barrels:** 1 HP, explode on destruction dealing 40 damage in 5-unit radius
- **Destruction effect:** Debris particles, dust cloud
- **Destroyed walls leave rubble** (flat low box on ground)

## 9. HUD (HTML overlay)

- **Top-left:** Health bar (green→red gradient)
- **Top-right:** Wave number, score
- **Bottom-center:** "WASD to move, mouse to aim, click to fire" (fades after 5s)
- **Wave announcement:** "WAVE N" text, center screen, fades in/out
- **Game over:** Score, wave reached, "Click to restart" button

## 10. File Structure

Single `index.html` containing:
- HTML: `<canvas>` + HUD overlay elements
- CSS: Fullscreen canvas, HUD positioning, animations
- JS: All game logic in `<script>` tags, organized by section comments

## 11. Performance Targets

- 60 FPS on modern browsers (Chrome, Firefox, Safari)
- Max ~50 entities active at once (player + enemies + bullets + particles)
- Particle pool with recycling (max 200 particles)
- Frustum culling via Three.js built-in
- No textures loaded from files — all materials are procedural/programmatic

## 12. Scope — What's NOT in v1

- No multiplayer
- No upgrade shop between waves
- No power-ups / pickups
- No multiple weapon types
- No sound effects (visual only)
- No save/load
- No mobile touch controls
