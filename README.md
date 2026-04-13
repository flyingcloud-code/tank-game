# Tank Battle — 3D Wave Survival

A browser-based 3D tank game with military-realistic visuals, isometric camera, and wave survival gameplay. Single HTML file, zero dependencies — just open and play.

## Play

Open `index.html` in any modern browser, or serve locally:

```bash
python3 -m http.server 8080
# → http://localhost:8080
```

## Controls

| Input | Action |
|-------|--------|
| WASD | Move tank |
| Mouse | Aim turret |
| Left Click | Fire cannon |

## Gameplay

- Survive waves of enemy tanks that get progressively harder
- Destroy brick walls and explosive barrels to open new paths
- Barrels deal area damage — use them tactically
- Scoring: 100 pts per kill + wave bonus

## Tech

- **Three.js** (CDN) — 3D rendering
- **Web Audio API** — procedural sound effects
- **ECS-lite** — entity-component architecture in vanilla JS
- Single `index.html` file, no build step, no dependencies
