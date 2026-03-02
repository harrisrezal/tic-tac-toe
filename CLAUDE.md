# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A single-file browser-based Tic Tac Toe game. The entire application lives in `tictactoe.html` — no build tools, no dependencies, no server required.

## Running the Project

```bash
open tictactoe.html          # macOS — open in default browser
```

There are no build steps, no package manager, and no test suite. Reload the browser tab to see changes.

## Git & GitHub Workflow

Every change must be committed and pushed to `https://github.com/harrisrezal/tic-tac-toe`.

```bash
git add tictactoe.html
git commit -m "short imperative summary"
git push
```

Commit messages should be short and imperative (e.g. `Add AI opponent`, `Fix turn-timer stacking on reset`). Use a multi-line body only when the change warrants explanation.

## Architecture

`tictactoe.html` is structured in three sections:

1. **CSS** (`<style>`) — all styling inline; dark theme (`#1a1a2e` background, `#e94560` for X, `#a8dadc` for O)
2. **HTML** — static markup: timer row → status → 3×3 board → New Game button → leaderboard table
3. **JavaScript** (`<script>`) — all game logic; no frameworks, no modules

### Key JS state variables

| Variable | Purpose |
|---|---|
| `board` | `Array(9)` of `null \| 'X' \| 'O'` |
| `current` | Active player (`'X'` or `'O'`) |
| `over` | Boolean; blocks clicks when `true` |
| `scores` | `{ X: {wins, losses, ties}, O: {wins, losses, ties} }` — session-only, resets on reload |
| `gameTimerInterval` | `setInterval` ref for the per-game stopwatch |
| `turnTimerInterval` | `setInterval` ref for the 15s per-turn countdown |

### Key JS functions

- `init()` — clears both intervals, resets board/state, starts both timers; called on load and "New Game"
- `startGameTimer()` / `stopGameTimer()` — manages the game stopwatch
- `startTurnTimer()` — clears any existing turn interval before starting a fresh 15s countdown; auto-forfeits turn at 0
- `stopTurnTimer()` — clears turn interval and shows `—`
- `checkWinner()` — checks `WINS` combos; returns `{winner, line}`, `{tie: true}`, or `null`
- `updateLeaderboard()` — reads `scores` and re-renders the leaderboard table cells

### Timer rules

- Always call `clearInterval` on both `gameTimerInterval` and `turnTimerInterval` before starting new ones — prevents interval stacking across resets
- `startTurnTimer()` calls `clearInterval(turnTimerInterval)` itself, so it is safe to call on every move
- Turn timer shows `—` and loses the `.urgent` class when the game ends
