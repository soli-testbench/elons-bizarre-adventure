# Plan: Remove Comm Dish Rock Resource Highlighting

## Problem
The Comm Dish structure highlights nearby tiles containing Rock Resources with a cyan overlay. This feature serves no gameplay purpose and should be removed.

## Technical Analysis

All relevant code is in `game.js`:

1. **`getCommDishRevealedTiles()`** (lines 737–771) — BFS from each comm dish up to 5 tiles, collecting tiles where `tile.resource === "rocks"` into a revealed set.
2. **`drawCommDishOverlay(revealedTiles)`** (lines 773–788) — Draws a cyan stroke + faint fill on each revealed tile.
3. **Call site in `render()`** (lines 800–802) — Calls both functions and draws the overlay every frame.

No other code references `getCommDishRevealedTiles` or `drawCommDishOverlay`. Removing all three locations is safe and complete.

## Approach

- Delete the two functions (`getCommDishRevealedTiles`, `drawCommDishOverlay`) entirely.
- Delete the 3-line call site in `render()`.
- No other files are affected. No tests exist in this project.

## Risks

None. The functions are self-contained and not called from anywhere else.
