# GC Surface Tension Game (Ruby.wasm) - Specification

## 1. Overview

This is a local multiplayer, turn-based browser game implemented in a single `index.html` file using `ruby.wasm`.

Players take turns entering exactly one line of Ruby code (IRB-like style).  
The game tracks object allocations and garbage collection (GC), then decides the winner by score.

## 2. Core Rules

1. Before starting, users configure:
   - Player list (2 or more players, default 3)
   - GC target count `n` (integer, default `1`, minimum `1`)
2. The game runs one shared Ruby VM for all players.
3. Each turn, the current player submits one Ruby line.
4. A line cannot be submitted while it has a syntax error.
5. For each submitted line:
   - The line is executed in `TOPLEVEL_BINDING`.
   - Allocation delta is measured as:
     - `alloc_delta = GC.stat[:total_allocated_objects]_after - _before`
   - GC delta is measured as:
     - `gc_delta = GC.stat[:count]_after - _before`
6. The player’s score increases by `alloc_delta`.
7. The game keeps a global `gcProgress` as the sum of all `gc_delta`.
8. When `gcProgress >= n`, the game ends immediately.
9. The player who triggered the final GC progress (the last turn player) gets score `0`.
10. If a turn execution exceeds 3 seconds, that player immediately gets score `0` and the game ends.
11. The highest score wins. If multiple players tie for highest score, the result is a draw.

## 3. Syntax Validation

1. Syntax is checked before submission using:
   - `RubyVM::InstructionSequence.compile(line)`
2. If syntax is invalid:
   - Submit button stays disabled.
   - Enter key does not submit.
   - Turn is not consumed.

## 4. Runtime Errors

1. Runtime exceptions do not block the turn from being processed.
2. Exception text is shown in the game log.
3. Even on exception, `alloc_delta` and `gc_delta` for that turn are still counted.

## 5. UI Requirements

1. Setup panel:
   - Player names textarea
   - GC target input (`n`)
   - Start and Reset buttons
2. Turn panel:
   - Current player
   - Turn number
   - One-line Ruby input
   - Syntax status
   - Submit button
   - GC progress display (`current / target`)
   - Scoreboard
3. Output area:
   - Main game log
   - Dedicated right-side `GC.stat` window showing the latest full `GC.stat`

## 6. Game State and Flow

1. On start:
   - Validate player count (`>= 2`)
   - Initialize scores to zero
   - Reset turn index, turn count, GC progress, log, and GC stat window
2. On each valid submission:
   - Execute line
   - Update score and GC progress
   - Update log and GC.stat window
   - Check end condition (`gcProgress >= n`)
   - If not ended, advance to next player
3. On end:
   - Apply last-player-zero rule
   - Compute winner(s)
   - Disable input and submit controls

## 7. Technical Constraints

1. Single-file app (`index.html`) for application logic/UI.
2. `ruby.wasm` and support script are loaded from CDN.
3. No remote multiplayer synchronization is included (local pass-and-play only).
