# Plan: View Hotkeys Modal

## Summary

Add an ESC-toggled hotkey overlay/modal to the Mars colony game that lists all active keybindings, blocks game input while visible, and follows existing UI conventions.

## Codebase Analysis

- **Stack**: Vanilla HTML/CSS/JS — no build system, no frameworks, no dependencies
- **Files**: `index.html`, `style.css`, `game.js` (single IIFE module)
- **Serving**: Static files via nginx (Dockerfile)
- **Existing keydown handler**: `game.js:339-374` — handles W/A/S/D, Arrow keys, G, Enter/E via a `switch` on `e.key`

### Current Hotkeys (from `game.js:344-369`)

| Key | Action |
|-----|--------|
| W / Arrow Up | Move up |
| S / Arrow Down | Move down |
| A / Arrow Left | Move left |
| D / Arrow Right | Move right |
| G | Gather resource |
| E / Enter | End turn |
| ESC | Toggle hotkey overlay (new) |

## Technical Approach

### Architecture

Since this is a vanilla JS/CSS/HTML project with no build system, the implementation stays within those constraints. The modal is a static HTML element in `index.html`, styled in `style.css`, and toggled via JS in `game.js`.

### Changes by File

#### 1. `index.html`
- Add a `<div id="hotkey-modal">` overlay element containing a table/list of all hotkeys
- Include a close button (X) in the modal header
- Place it as a sibling to `#game-container` inside `#app`

#### 2. `style.css`
- Add styles for `#hotkey-modal`: full-viewport overlay with semi-transparent backdrop
- Style the modal content box using existing `.panel` patterns (background `#2a1515`, border `#3a2020`, heading color `#cc7744`)
- Use the existing color palette: `#ff8844` for accent, `#e0d0c0` for text, `#1a0a0a` tones for backgrounds
- Add `.hotkey-table` styles for the key-description listing
- Modal hidden by default (class `.hidden` with `display: none`)

#### 3. `game.js`
- Add `state.hotkeyModalOpen = false` to the state object
- Add `toggleHotkeyModal()` function that shows/hides the modal element and flips the state flag
- Modify the keydown handler: check `state.hotkeyModalOpen` first — if true, only handle ESC (to close), ignore everything else
- Add ESC to the switch statement to call `toggleHotkeyModal()`
- Wire up the close button click handler

### Input Blocking Strategy

The keydown listener already uses a `switch` with `default: return`. The approach:
1. At the top of the keydown handler, check if `state.hotkeyModalOpen` is `true`
2. If modal is open and key is ESC → close modal, `e.preventDefault()`, return
3. If modal is open and key is anything else → return (swallow the input)
4. Otherwise, proceed with existing switch logic, adding ESC as a new case

This blocks all game keyboard input while the modal is open without changing any existing game logic.

## Design Direction

**"Mars HUD" — matches existing dark rust theme**

The modal uses the same visual language as the sidebar panels:
- Dark rust background (`#2a1515`) with subtle border (`#3a2020`)
- Orange accent headings (`#cc7744`, `#ff8844`)
- Warm beige text (`#e0d0c0`)
- Semi-transparent black backdrop overlay
- Minimal, functional layout — no gratuitous animations

## Tech Debt Notes

- The project uses vanilla JS with no module system or build tooling. This is noted but not addressed — it's out of scope.
- All hotkeys are hardcoded in both the keydown handler and the modal HTML. A data-driven approach (single source of truth for keybindings) would be better but is over-engineering for this scope.

## Risks & Mitigations

- **Risk**: Canvas click events could still fire while modal is open → **Mitigation**: The overlay covers the canvas, so clicks are naturally blocked by the overlay element.
- **Risk**: Button clicks in sidebar still work → **Mitigation**: The overlay covers the entire viewport, blocking all pointer interactions beneath it.

## Task Decomposition

**1 task** — this is a small, self-contained feature touching 3 tightly coupled files. No parallelism benefit.
