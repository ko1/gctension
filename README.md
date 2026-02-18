# GC Surface Tension Game

A local pass-and-play browser game powered by `ruby.wasm`.  
Players submit one Ruby line per turn in a shared VM.  
If you trigger GC or exceed 3 seconds, you are eliminated.

## Files

- `index.html`: Main app (UI + game logic)
- `spec.md`: Detailed specification (English)

## Quick Start

1. Start a simple local web server in this directory.
2. Open the app in your browser.
3. Set player names, then start the game.

Example:

```bash
cd /home/ko1/app/gctension
python3 -m http.server 8000
```

Open `http://localhost:8000` in your browser.

## Rules (Short)

1. Players take turns entering exactly one line of Ruby.
2. Lines with syntax errors cannot be submitted.
3. If a turn triggers GC, that player is eliminated.
4. If a turn takes more than 3 seconds, that player is eliminated.
5. Turn order reverses every round.
6. Failure target is auto-calculated as `n = max(1, players - 1)`.
7. Game ends when one survivor remains (or no survivors).
8. Game log shows per-turn allocated object delta (`+N objects`).
9. Syntax checks run in a separate Ruby VM worker.

## Heap Panel

- Right panel shows heap as a live/free bar + recent trend.
- Live/free are shown only as integer percentages.
- Current `GC count` is shown.
- `heap_available_slots` is shown as a numeric value.
