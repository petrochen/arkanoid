# ARKANOID

Browser recreation of the 1986 Taito arcade classic. Single HTML file, no dependencies, no build step.

**Play:** https://arkanoid.petrochenko.info

Part of the retro arcade collection: [Tetris](https://tetris.petrochenko.info) | [Pac-Man](https://pacman.petrochenko.info) | [Space Invaders](https://invaders.petrochenko.info) | **Arkanoid**

## Arcade-Accurate Mechanics

Every mechanic has been verified against the original 1986 Taito cabinet:

### Paddle (Vaus)

- Width: 32px standard, expands to 48px with the E power-up
- Horizontal movement clamped to screen edges
- **Hit position determines reflection angle** — center hit = steep vertical, edge hit = sharp diagonal
- In Laser mode, fires projectiles from both ends of the paddle
- In Catch mode, ball sticks on contact; aim and release with Space/FIRE

### Ball Physics

- 4px ball with physics-based angle reflection
- **Continuous collision detection** — no tunneling through thin bricks at high speed
- Reflection angle calculated from normalized hit offset on paddle surface
- Wall bounces off left, right, and top edges; bottom edge = ball lost

### Brick Types

| Color  | Points | Hits | Notes                     |
| ------ | ------ | ---- | ------------------------- |
| White  | 50     | 1    | Standard                  |
| Orange | 60     | 1    | Standard                  |
| Cyan   | 70     | 1    | Standard                  |
| Green  | 80     | 1    | Standard                  |
| Red    | 90     | 1    | Standard                  |
| Blue   | 100    | 1    | Standard                  |
| Pink   | 110    | 1    | Standard                  |
| Yellow | 120    | 1    | Standard                  |
| Silver | 150    | 2    | Flashes on first hit      |
| Gold   | 0      | ∞    | Indestructible, no points |

### Power-Ups

Power-ups drop from destroyed bricks and fall at constant speed. Collect by letting the paddle touch them:

| Capsule | Name    | Effect                                              |
| ------- | ------- | --------------------------------------------------- |
| **S**   | Slow    | Reduces ball speed                                  |
| **C**   | Catch   | Ball sticks to paddle on contact; release with fire |
| **E**   | Expand  | Paddle widens from 32px to 48px                     |
| **D**   | Disrupt | Splits ball into 3 simultaneous balls               |
| **L**   | Laser   | Paddle fires laser projectiles; press fire to shoot |
| **B**   | Break   | Opens an exit warp — clear the level instantly      |
| **P**   | Player  | Awards an extra life                                |

Only one power-up mode is active at a time; collecting a new one replaces the current effect.

### Scoring

- Points awarded per brick destroyed (see Brick Types table above)
- Silver bricks score only on the final destroying hit
- Gold bricks award no points
- Extra life via P power-up

### Levels

- 10 unique hand-crafted layouts with gold and silver brick arrangements
- After level 10, loop restarts with increased ball speed
- B (Break) power-up skips the current level instantly

## Controls

| Action        | Desktop           | Mobile               |
| ------------- | ----------------- | -------------------- |
| Move paddle   | Arrow keys / A, D | LEFT / RIGHT buttons |
| Launch / Fire | Space             | FIRE button          |
| Pause         | Esc / P           | --                   |
| Help          | H                 | Tap help overlay     |

D-pad is always visible (works with Bluetooth keyboard connected to phone).

## Sound

All synthesized via Web Audio API, no audio files:

| Sound              | Trigger                               |
| ------------------ | ------------------------------------- |
| **Paddle hit**     | Ball contacts paddle surface          |
| **Brick hit**      | Ball destroys or bounces off a brick  |
| **Power-up catch** | Paddle collects a falling capsule     |
| **Laser fire**     | Laser projectile launched from paddle |
| **Ball lost**      | Ball exits through the bottom edge    |
| **Level complete** | All destructible bricks cleared       |

## Tech Stack

- **Rendering:** Canvas 2D API
- **Audio:** Web Audio API synthesizer
- **Hosting:** Cloudflare Pages
- **Size:** Single `index.html` (~1,800 lines), zero dependencies

## Responsive Layout

| Device                        | D-pad Size           | Notes                        |
| ----------------------------- | -------------------- | ---------------------------- |
| Small phone (< 380px)         | 52px                 | Compact                      |
| Medium phone (420px+)         | 62px                 | Standard                     |
| Large phone / tablet (480px+) | 74px                 | Comfortable                  |
| Desktop                       | 52px + keyboard hint | Both input methods available |

## Leaderboard

- Scores submitted automatically to `arcade-api.petrochenko.info` on game over
- Player name saved locally and reused across sessions
- Global leaderboard visible from the game UI

## Deploy

```bash
npx wrangler pages deploy . --project-name arkanoid --branch main --commit-dirty=true
```

## Architecture

Everything lives in `index.html` (~1,800 lines):

1. **CSS** — responsive layout, overlays, D-pad, color scheme
2. **HTML** — canvas, score header, lives row, overlays (start/gameover/help), D-pad
3. **Constants** — dimensions, speeds, brick color map, point values, power-up types
4. **Game State** — score, lives, level, ball list, active power-up, timers
5. **Level Data** — 10 brick layout grids with brick type codes
6. **Brick System** — grid render, hit detection, silver two-hit tracking, gold immunity
7. **Ball Physics** — velocity vector, wall bounce, angle-from-offset paddle reflection
8. **Collision Detection** — pixel-level continuous detection for ball vs. brick and paddle
9. **Power-Up System** — spawn on brick destroy, fall, collect, apply/replace effect
10. **Laser Mode** — projectile creation, upward movement, brick collision
11. **Catch Mode** — ball offset lock to paddle, release on fire input
12. **Multi-Ball** — D power-up splits current ball into 3; level ends when all balls lost
13. **Scoring** — per-brick points, silver final-hit rule, P power-up extra life
14. **Drawing** — bricks, paddle, ball(s), falling capsules, laser bolts, UI elements
15. **Input** — keyboard (arrows/WASD/space), D-pad (touch/pointer), pause (Esc/P), help (H)
16. **Audio** — Web Audio: paddle hit, brick hit, power-up, laser, ball lost, level complete
17. **Leaderboard** — fetch/submit via arcade-api.petrochenko.info, name persistence
18. **Game Loop** — fixed timestep (1000/60), accumulator, state machine (start/playing/paused/dead/levelcomplete/gameover)

## Arcade Accuracy Notes

| Mechanic          | Original (1986 Taito)                          | This Recreation                                |
| ----------------- | ---------------------------------------------- | ---------------------------------------------- |
| Paddle width      | 32px standard, 48px expanded                   | Matched exactly                                |
| Ball size         | 4px                                            | Matched exactly                                |
| Reflection angle  | Offset-based, not pure reflection              | Normalized hit position drives angle           |
| Silver bricks     | 2 hits, flash on first                         | Flash animation on first hit, destroyed second |
| Gold bricks       | Indestructible throughout level                | Permanent, no damage                           |
| Power-up override | New capsule replaces active effect immediately | Matched                                        |
| Multi-ball loss   | Level continues until last ball is lost        | All balls tracked; life lost when all gone     |
| Level loop        | Restarts from level 1 with higher speed        | Speed increment applied on each loop           |

## License

For educational purposes. Arkanoid is a trademark of Taito Corporation / Square Enix.
