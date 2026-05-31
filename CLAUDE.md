# CLAUDE.md — Movement Lab

You are working in the **Movement Lab** repo: a standalone, reusable tool for
authoring and tuning 2D character movement and feel, separate from any game.

## What this is
A single self-contained HTML file (`index.html`) — vanilla JS + HTML5 Canvas — that
runs by being opened in a browser. It is a greybox harness: mechanics live in
primitive shapes; real art is never required to test feel. Characters tuned here are
meant to be exported and imported into games.

## Non-negotiable conventions (do not break these)
- **No build step, no framework, no bundler.** It must run by opening `index.html`.
- **Self-contained & offline.** No network calls, no CDN imports, no `localStorage`/
  `sessionStorage`. All assets/state embedded or in-memory.
- **Feel lives in a labeled constants block** (`CHARACTER` + the live mirror `P`).
- **Greybox first.** Shapes for mechanics; art is swapped in only at the draw layer.
- **Hitboxes are code, not art** — defined in code, inset from the visual.
- **Pixel art, if any, renders crisp:** `ctx.imageSmoothingEnabled = false`.

## The two load-bearing ideas — preserve them in any change
1. **Character package** (`CHARACTER`): a character is data the actor reads from —
   `size`, `hitbox`, `movement` (runSpeed, accel, friction, airControl, gravity,
   jumpPower, jumpCut, maxFall, coyoteFrames, bufferFrames), and `anim` (mode,
   stepDistance, cadence, footLift, legLen). The actor must not hardcode values that
   belong in the package.
2. **Environment contract** (`ENV.query*`): the level exposes `queryGround`,
   `queryLedge`, `pastFallLine`, plus `solidsHit` / `platformsHit`. The character
   touches the world ONLY through these. Keeping this boundary clean is what makes
   characters portable between environments and projects.

## Movement model (already implemented — match it when extending)
- Axis-separated AABB collision (resolve X, then Y).
- One-way (pass-through) platforms; drop through with Down.
- Variable-height jump with **coyote time** and **jump buffering**.
- Ledge grab/hang/release via the contract.
- Blast zone below the stage → respawn (so testing loops without reload).
- **Plant-locked, distance-driven gait** is the default; a time-driven mode exists
  purely to demonstrate the skating failure. Distance-driven is correct.

## How to extend (typical future tasks)
- **Add a character:** add a new package object in the package shape; let the actor
  read from it. Eventually: load an exported `package.json` + sprite atlas.
- **Add an environment:** add a layout that satisfies the contract (`query*`). The
  character should work in it unchanged.
- **Add a state:** extend the animation spec + state machine (e.g. `land`, `dash`).
- **Export path (planned):** a character = `package.json` (this shape) + a sprite
  atlas (`atlas.png` + `atlas.json`). A game imports it by satisfying the contract.

## Workflow
This repo — not any chat — is the source of truth for the lab. Make focused commits.
Design discussion happens elsewhere; decisions land here as code + docs.
