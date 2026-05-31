# Movement Lab

A standalone, greybox test harness for authoring and tuning how a 2D character
**moves and feels**, decoupled from any single game. Built to be reused across
projects: tune a character here, then export it and drop it into a game.

Vanilla JavaScript + HTML5 Canvas. No framework, no build step, no network calls,
no storage. **Run it by opening `index.html` in a browser.** That's the whole setup.

## Controls
- Move: `←` `→` or `A` `D`
- Run: hold `Shift`
- Jump: `Space` / `↑` / `W` (hold for a higher jump)
- Drop through a one-way platform / release a ledge: `↓` / `S`
- Grab a ledge: fall toward a stage edge while holding toward it
- Reset position: `R`

## What it's for
The lab exists to answer one question fast: *does this character feel good to move?*
The right-side panel tunes feel live (no code edits) and instruments everything —
state, velocity, foot-plant ticks, the collision-contract probes.

The headline control is **DISTANCE-DRIVEN vs TIME-DRIVEN** animation. Distance-driven
locks the stride to ground speed so the feet don't skate; time-driven runs the stride
on a clock and skates at speed. That coupling is the difference between movement that
reads as smooth and movement that reads as choppy.

## Architecture (see `docs/character-package.md`)
- **Character package** — a character is *data* the actor reads from (size, hitbox,
  movement constants, animation spec). Swap the package → different character.
- **Environment contract** — the level only answers queries (ground? wall? ledge?
  past the fall line?). Any environment that answers them can host any character.

## Repo conventions
This repo is the source of truth for the lab. Don't reintroduce a build step, a
framework, network calls, or browser storage. Keep it a single openable file.
