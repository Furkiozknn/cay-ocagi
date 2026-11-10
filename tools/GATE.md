# tools/gate.mjs: bot regression gate

`tools/smoke.mjs` answers "does the page load with 0 errors". `tools/gate.mjs` answers "does the game still play as
well as before, and can a phone reach its buttons". It exists because on 9 Nov an unreviewed commit made arrows do
0 damage to armoured enemies: the bot fell from wave 15 to wave 8 and smoke stayed green.

## What it checks

1. **sim**: loads the build in headless Chromium with `?auto&debug`, holds `requestAnimationFrame` so wall-clock
   frames cannot advance the game, calls the game's own fast-forward hook (`hisarSim(1260)`) and reads the metrics.
   The candidate runs twice; different numbers mean the sim is not deterministic and the gate fails (a comparison
   would be meaningless). The base runs once (skipped when it is byte-identical to the candidate). Each metric is
   compared with its tolerance (`abs` units or `rel` fraction it may drop, `max` ceiling, `info` not gated).
2. **layout**: at 390×844, 360×740 and 412×915 with `isMobile` + `hasTouch`, every visible element matching
   `GAME.layout.selectors` must be inside the viewport and be what `elementFromPoint` returns at its centre.
3. **console**: every run (sim and layout) must have 0 page errors, 0 console errors and 0 failed requests.
   Analytics and Google Fonts are answered with an empty 200, as in smoke.

Output: `ok` / `FAIL` / `KNOWN` / `note` lines, then `RESULT OK|FAIL`, exit 1 on FAIL. `GATE_JSON=<file>` also writes
a JSON report.

## Running

```sh
node tools/gate.mjs                       # base = origin/main:index.html, candidate = working tree
node tools/gate.mjs --base <ref>          # any git ref
node tools/gate.mjs --baseline            # base = tools/gate-baseline.json (no git base needed)
node tools/gate.mjs --record              # write tools/gate-baseline.json from the working tree (main only)
node tools/gate.mjs --candidate x.html    # test a scratch build without touching index.html
node tools/gate.mjs --only sim|layout
node tools/gate.mjs --strict-layout       # KNOWN layout offenders fail too
SMOKE_CDN_DIR=<npm package dir> ...       # serve three.js from a local copy (no CDN access)
```

Assets (models, images) always come from the working tree; only the entry HTML differs between base and candidate.
Runtime here (4 cores, shared, load average 25-35): 40-80 s per sim run; a full gate is about 4.5 min when the base is
identical (2 sim runs) and up to 9 min with a different base (3 sim runs); `--baseline --only sim` about 3 min.
Timeouts are generous and print as `infra: … timed out` (still a FAIL, never a pass); a whole-run watchdog stops at
15 min. CI runs it as the `gate` job in `.github/workflows/smoke.yml` (PR: base = `HEAD^1`, the base
branch; manual run: `--baseline`).

## This game (cay-ocagi)

Copied from kale-savunmasi's `tools/gate.mjs`. The `GAME` block differs, plus one small generic addition: an
optional `GAME.sim.init` function that runs before the page's own scripts (`page.addInitScript`). The sections above
describe the kale-savunmasi original (`hisarSim`, its timings); for cay-ocagi:

- **sim**: `?auto&noassets` (bot on, nothing saved, procedural look). `run` calls the game's own
  `cayBench(12, 3)`: 12 full bot campaigns of 7 days in 60 Hz fixed steps, the sim RNG reseeded to `3000 + i` per
  game (the research report's balance measure, 12/12 wins). `cayBench` returns text only, so `run` parses its summary.
- **determinism needs `init`**: without it two loads gave 11 vs 12 wins. The sim RNG is seeded, but the cat's first
  nap is `14 + Math.random() * 10` s (`const cat` in index.html); when the cat wakes it draws from the sim RNG and
  shifts every later customer. `init` replaces `Math.random` with a fixed-seed generator in the sim page only, so the
  whole page is reproducible (it also fixes particles and texture jitter, which do not touch the sim). The real fix
  belongs in the game (cat timer from `rr()`, or `cayBench` resetting the cat as the daily mode already does); then
  `init` can go.
- **metrics**: `wins` (abs 1 of 12), `starsSum` and `ratioSum` (sum over days 1-7 of the per-day average stars and
  coins/target; rel 10 %) are gated; `daysPlayed`, `dayFails`, `angrySum`, `walkbySum` are info; `gameErrors`
  (`window.cay.errors`) max 0. Main ca336f0: 12 wins, 84 days, starsSum 18, ratioSum 11.59.
- **layout**: `?auto&sim=5` fast-forwards 5 s before the first frame, so the bot is already in day 1: `#top` (speed,
  pause, sound, settings, language) and `#dock` (tray slots and stations). Stations not unlocked yet carry `.st.lock`
  (`pointer-events: none` by design) and are excluded. No offenders on main, so `layout.known` is empty.
- **runtime here**: about 30 s per sim run; full gate with an identical base about 2.5 min.

## Porting to another game

Copy `tools/gate.mjs` and edit only the `GAME` block at the top:

- `entry`, `cdn.match` / `cdn.pkg` (import-map CDN URL and the part before the path inside the npm package),
  `ignored` (requests answered empty).
- `sim.query`: URL flags that start the bot and expose the hook (`?auto&debug`, `?auto&sim`, …).
- `sim.ready` / `sim.run` / `sim.metrics`: in-page functions. `run` must be synchronous (or return a promise) and
  deterministic: fixed time step, seeded RNG, bot on. If the game only has a `?sim=N` URL flag that runs before the
  first frame, put it in `query` and make `run` a no-op. If the core runs in Node, a Node runner is cheaper, but keep
  the same output format.
- `sim.tolerance`: which metrics gate and how much they may drop. Prefer the bot's progress measure (wave, level,
  stars, day), a volume measure (kills, score) and an economy measure (gold, cash). Add the game's own error count
  with `max: 0`.
- `sim.holdFrames`: keep `true` unless the hook needs the render loop.
- `layout.query` / `layout.selectors`: a URL that shows the in-game HUD with no overlay, and the HUD's buttons.
- `layout.known`: only real, already-reported offenders, with the commit and the reason. Never add one to make a
  run green.

Checklist per game:

1. Find the hook (`grep -n "Sim\b\|?sim\|?auto\|selftest" index.html`) and check in the code that it uses a fixed step
   and a seeded RNG (no `Math.random`/`Date.now` in the update path).
2. Run `--only sim` twice on main: the determinism line must say `identical`.
3. Check the numbers against the game's README/report claims; if they differ, write down which is right.
4. `--record` on main and commit `tools/gate-baseline.json`.
5. Prove it fails: a scratch `--candidate` with a known regression (e.g. a revert of a bad relay commit, or damage × 0)
   must give `RESULT FAIL` on a sim metric. Keep the scratch file out of git.
6. Run the layout part; list existing offenders in `layout.known` with their source commit, or fix them first.
7. Add the `gate` job to the game's workflow (chromium only, `timeout-minutes`, `permissions: contents: read`,
   the already pinned `actions/checkout` and `actions/setup-node` SHAs, `fetch-depth: 2`).
