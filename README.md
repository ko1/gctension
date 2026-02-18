# GC Surface Tension Game

A local pass-and-play browser game powered by `ruby.wasm`.  
Players submit one Ruby line per turn, and the winner is decided by allocation-based scoring with GC-driven end conditions.

## Files

- `index.html`: Main app (UI + game logic)
- `spec.md`: Detailed specification (English)

## Quick Start

1. Start a simple local web server in this directory.
2. Open the app in your browser.
3. Set player names and GC target count `n`, then start the game.

Example:

```bash
cd /home/ko1/app/gctension
python3 -m http.server 8000
```

Open `http://localhost:8000` in your browser.

## Rules (Short)

1. Players take turns entering exactly one line of Ruby.
2. Lines with syntax errors cannot be submitted.
3. On each turn, `alloc_delta` is added to the current player's score.
4. The game ends when total GC progress reaches target `n`.
5. The player who triggers the final GC gets score `0`.
6. If a turn takes more than 3 seconds, that player gets score `0`.
7. The highest score wins (ties are a draw).
