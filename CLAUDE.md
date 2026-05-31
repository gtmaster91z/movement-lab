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
- **Persistence is file-based, not network/storage.** Characters save and load as
  JSON packages via user-initiated download (export) and a file picker (import) —
  that's the allowed persistence model. Do NOT add auto-saving browser storage or a
  backend without an explicit, deliberate decision to break the offline rule.
- **Never `fetch()` a character at startup.** Browsers block `fetch()` on `file://`,
  so loading a character over the network would break local double-click use. Built-in
  characters live EMBEDDED in `index.html` (`PRESETS`); external ones come in through
  the import file picker. The `characters/*.json` files are version-controlled
  artifacts/references, not runtime-fetched.
- **Feel lives in a labeled constants block** (`PRESETS` / `activeChar` + the live
  mirror `P`).
- **Greybox first.** Shapes for mechanics; art is swapped in only at the draw layer.
- **Hitboxes are code, not art** — defined in code, inset from the visual.
- **Pixel art, if any, renders crisp:** `ctx.imageSmoothingEnabled = false`.

## The two load-bearing ideas — preserve them in any change
1. **Character package** (`PRESETS` entries / `activeChar`): a character is data the
   actor reads from — `size`, `hitbox`, `movement` (runSpeed, accel, friction,
   airControl, gravity, jumpPower, jumpCut, maxFall, coyoteFrames, bufferFrames), and
   `anim` (mode, stepDistance, cadence, footLift, legLen). The actor must not hardcode
   values that belong in the package. `PRESETS` is the built-in library; `activeChar`
   is the currently loaded package; `P` is the live mirror the sim reads each frame.
   `applyPackage()` loads a package into the lab; `currentPackage()` + export writes
   one back out; `validatePackage()` sanitizes/clamps anything imported.
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
- **Add a character:** add a new entry to `PRESETS` (embedded) and/or import a JSON
  package at runtime. Keep a version-controlled copy under `characters/`.
- **Add an environment:** add a layout that satisfies the contract (`query*`). The
  character should work in it unchanged.
- **Add a state:** extend the animation spec + state machine (e.g. `land`, `dash`).
- **Add an ability (planned — Step 2):** mechanics like double-jump or dash are NOT
  just numbers — extend the package with an `abilities` block (flags/counts) AND teach
  the actor's state machine the mechanic, gated by those flags. Data-driven tuning is
  easy; a new mechanic needs actor code that the package then switches on/off.
- **Export path:** JSON-package export/import is DONE. Still planned: a sprite atlas
  (`atlas.png` + `atlas.json`) so a character = `package.json` + atlas. A game imports
  it by satisfying the contract.

## Workflow
This repo — not any chat — is the source of truth for the lab. Make focused commits.
Design discussion happens elsewhere; decisions land here as code + docs.
