# The Character Package & the Environment Contract

These two specs are the whole portability story. A character authored in the lab is
meant to travel: tune it here, export it, import it into a game that honors the
contract.

## Character package (the portable unit)
A character is data. In the lab this is the `CHARACTER` object; the export target is
a `package.json` of the same shape, paired with a sprite atlas.

Fields:
- `name`            — string id.
- `size`            — `{ w, h }` collision box in world px.
- `hitbox`          — `{ inset }` gameplay box, inset from the visual.
- `movement`:
  - `runSpeed`      — top horizontal speed (walk is a fraction of this).
  - `accel`         — ground acceleration toward top speed.
  - `friction`      — per-frame velocity retention with no input (0–1).
  - `airControl`    — fraction of ground accel usable midair (0–1).
  - `gravity`       — downward accel per frame.
  - `jumpPower`     — initial upward velocity on jump.
  - `jumpCut`       — velocity kept on early jump release (short hop; 0–1).
  - `maxFall`       — terminal downward velocity.
  - `coyoteFrames`  — frames after leaving ground a jump still works.
  - `bufferFrames`  — frames before landing a pressed jump is remembered.
- `anim`:
  - `mode`          — "distance" | "time" ("distance" is correct).
  - `stepDistance`  — world px traveled per stride (distance mode).
  - `cadence`       — gait phase advance per frame (time mode; demo only).
  - `footLift`      — swing-foot lift height.
  - `legLen`        — rig leg length.
  - (planned) `states` — frame sequences + per-state timing for: idle, walk, run,
    jump, fall, land, hang, turn.

## Environment contract (the only character↔world interface)
A character touches the world ONLY through these queries. Any environment that
implements them can host any character.

- `queryGround(x, y, w) -> surfaceTopY | null` — is there a surface under the feet?
- `queryLedge(x, y, w, h, facing, vy) -> ledge | null` — near a grabbable ledge while
  falling toward it?
- `pastFallLine(y) -> bool` — below the blast/fall line?
- `solidsHit(x, y, w, h) -> solids[]` — full-collision rects overlapping a box.
- `platformsHit(x, y, w, h) -> platforms[]` — one-way rects overlapping a box.

## Export / import

### In the lab today (implemented)
- **Built-in library:** the `PRESETS` object in `index.html` holds full packages,
  embedded so the lab runs offline by double-click. Pick one from the **preset**
  dropdown. Ships with `basic` (the working baseline) and `greybox_legacy` (the
  original feel, kept as a reference). `characters/*.json` mirrors these as
  version-controlled files.
- **Export:** the **EXPORT .JSON** button serializes the live state (a package of the
  shape above) and downloads `<name>.json`. Works locally and on the hosted site.
- **Import:** the **IMPORT .JSON** button opens a file picker, reads the file with
  `FileReader`, `JSON.parse`s it (never `eval`), then `validatePackage()` fills any
  missing fields from `basic` and **clamps every value to the slider ranges** before
  applying. Malformed files are rejected without disturbing the running sim.
- **Why files, not storage/cloud:** keeps the tool self-contained and offline, with no
  account or backend and no attack surface. A character is just a small JSON file you
  can version, hand to a friend, or drop into a game.
- **`file://` constraint:** characters are never `fetch()`ed at startup (browsers block
  that on `file://`); built-ins are embedded, external ones arrive via the file picker.

### Still planned
- **Sprite atlas:** pair the `package.json` with `atlas.png` + `atlas.json` (frames,
  trims, baseline/anchor, derived hitbox) so a character = package + atlas.
- **Abilities:** an `abilities` block (e.g. double-jump, dash) — needs matching actor
  state-machine support, gated by the flags (see CLAUDE.md "Add an ability").
- **Import into a game:** the game provides an environment that satisfies the contract
  and an actor that reads the package. No character code is rewritten on import.
