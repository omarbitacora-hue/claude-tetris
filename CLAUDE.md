# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the game

No build step. Open directly or serve locally:

```bash
open index.html                  # macOS
python3 -m http.server 8000      # then visit http://localhost:8000
```

## Architecture

Three files, no dependencies, no bundler:

- `index.html` — DOM structure: `<canvas id="board">` (300×600px) for the board, `<canvas id="next-canvas">` (120×120px) for piece preview, `#overlay` div for pause/game-over states.
- `style.css` — dark/retro theme using flexbox, CSS variables, and `backdrop-filter`.
- `game.js` — all game logic (~300 lines). Key internals:
  - **Board**: `ROWS×COLS` matrix; `0` = empty, `1–7` = piece color index.
  - **Pieces**: defined as square matrices; `rotateCW` = transpose + reverse rows.
  - **Collision**: `collide()` checks bounds and overlap against locked cells.
  - **Wall kicks**: `tryRotate()` attempts ±1, ±2 column offsets before discarding rotation.
  - **Game loop**: `requestAnimationFrame`-based; accumulates `dt` and drops piece when `dt ≥ dropInterval`.
  - **Line clear**: `clearLines()` scans bottom-up, splices full rows, unshifts empty row at top.
  - **Speed**: `dropInterval = max(100, 1000 − (level−1) × 90)` ms; level up every 10 lines.
  - **Ghost piece**: projected downward at `globalAlpha = 0.2`.

## Tunable constants in `game.js`

| Constant | Default | Note |
|---|---|---|
| `COLS` / `ROWS` | 10 / 20 | If changed, update canvas `width`/`height` in `index.html` (`COLS×BLOCK` / `ROWS×BLOCK`) |
| `BLOCK` | 30 | Pixel size per cell |
| `COLORS` | 7 colors | One per piece type |
| `LINE_SCORES` | `[0,100,300,500,800]` | Points for 1–4 line clears, multiplied by level |
