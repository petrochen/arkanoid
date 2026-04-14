# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Single-file browser Arkanoid game. No build step, no dependencies. The entire game is `index.html`.

Live: https://arkanoid.petrochenko.info (Cloudflare Pages, project name `arkanoid`)

## Deploy

```bash
npx wrangler pages deploy . --project-name arkanoid --branch main --commit-dirty=true
```

## Design Decisions (lessons from Tetris, Pac-Man, Space Invaders)

1. All coordinates are integers — no fractional positions
2. State transitions via frame counters — no setTimeout
3. Move-first, then collide
4. One movement owner per entity per frame
5. HTML overlay for start/gameover — real button, blur after click
6. Skip game keys when INPUT is focused (leaderboard name)
7. initGame sets ALL vars before dependent functions
8. D-pad always visible (BT keyboard still works)
9. Ball physics uses continuous collision (not discrete) to prevent tunneling

## Game Specs (1986 Taito Arkanoid)

- **Playfield**: 224x256 (11 cols x 18 rows of bricks)
- **Paddle (Vaus)**: 32px wide (48px expanded), 8px tall
- **Ball**: 4px diameter, reflects based on paddle hit position
- **Bricks**: 16px wide x 8px tall, colored by type, 1-hit (most), some multi-hit
- **Power-ups**: S(slow), C(catch), E(expand), D(disrupt/3-ball), L(laser), B(break/skip), P(extra life)
- **Lives**: 3, extra via P power-up
- **Levels**: 10+ unique brick layouts
- **Leaderboard**: arcade-api.petrochenko.info
