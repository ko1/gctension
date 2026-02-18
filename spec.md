# GC Surface Tension Game (Ruby.wasm) - Specification

## 1. Overview

This is a local pass-and-play, turn-based browser game implemented in a single `index.html` using `ruby.wasm`.

Players enter one line of Ruby per turn in a shared VM state.
The game is elimination-based (no score system).

## 2. Core Rules

1. Before starting, users provide player names (minimum 1).
2. The game auto-calculates failure target:
   - `n = max(1, number_of_players - 1)`
3. Each turn, the current player submits exactly one Ruby line.
4. A player is eliminated if their submitted line causes GC (`gc_delta > 0`).
5. A player is eliminated if execution exceeds 3 seconds.
6. Turn order is processed in rounds:
   - Round 1: forward order
   - Round 2: reverse order
   - Round 3: forward order
   - ... (alternates each round)
7. The game ends when either:
   - only one survivor remains, or
   - elimination count reaches `n`.
8. The last surviving player wins.
9. Solo mode (`1` player):
   - one safe turn completes the game.

## 3. Syntax Validation

1. Syntax is validated before submission with `RubyVM::InstructionSequence.compile`.
2. Syntax-invalid lines cannot be submitted.
3. Invalid input does not consume a turn.

## 4. Runtime Behavior

1. Runtime exceptions are shown in the log and still count as a consumed turn.
2. Runtime exceptions do not automatically eliminate a player (unless GC is triggered).
3. Timeout (`>3s`) is handled by JS-side timeout control and worker termination.

## 5. UI Requirements

1. Setup panel:
   - Player names textarea
   - Auto-rule text for `n`
   - Start / Reset buttons
2. Turn panel:
   - Current player
   - Turn count
   - One-line Ruby input
   - Syntax status
   - Submit button
   - Failure progress (`failures / n`)
   - Player status board (`ALIVE` / `OUT`)
3. Output area:
   - Main game log
   - Dedicated `GC.stat` window (latest turn result)
4. End effects:
   - Failure-focused visual overlay on elimination finish events.

## 6. Technical Constraints

1. Single-file app logic in `index.html`.
2. `ruby.wasm` runtime loaded from CDN.
3. Ruby VM runs in a Web Worker for non-blocking execution and timeout control.
4. No remote multiplayer sync (local pass-and-play only).
