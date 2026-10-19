# Game 02: Çay Ocağı (tea house time-management)

Same six-block structure as game 01 (World, Life, Game rules, Interaction, HUD, Extras, Performance, Self-test).
Written by the orchestrator; the game itself is built by a different model each time (see the series table).

```text
Build a single-file browser game (one index.html with inline CSS and JS) using Three.js r170 loaded
as an ES module from https://cdn.jsdelivr.net/npm/three@0.170.0/ (import map; addons from examples/jsm
if needed). A 3D time-management game called "Çay Ocağı" (Turkish for a small tea house). Do not ask
questions, build it end to end.

WORLD: a low-poly isometric corner of an Istanbul-style seaside street at golden hour, built entirely
in code, no external models, textures or fonts beyond one Google Font. A tiny tea house with an awning,
a counter with a double-stacked tea kettle on a stove (semaver), a simit oven, a Turkish coffee
station with a sand-heated pot, a sink for dirty glasses, shelves of tulip-shaped tea glasses; outside,
small tables with stools on the cobbled pavement, a lamp post, a cat sleeping on a step, potted
geraniums, a strait with a ferry passing in the distance, seagulls. Clean toy-like clay look, flat
shading, soft shadows, one accent colour (#C2410C tea-amber) for UI highlights and selection.

LIFE: customers walk in along the pavement, take a free table, think, then show an order bubble
(1-3 items: tea, simit, Turkish coffee, ayran) with a patience bar. Tea must brew before it can be
poured; simit must be warmed; coffee must cook without boiling over. When served everything, they eat
and drink, leave money plus a tip based on remaining patience, and leave dirty glasses that must be
cleared before the table is free again. Impatient customers leave angry. The cat wakes up and wanders,
the ferry crosses, gulls circle.

GAME RULES: a day lasts 3 minutes of game time with a coin target; 7 days, each busier and adding a new
menu item or customer type (a hurried office worker with short patience, a group of 3, a regular who
tips double). Between days a shop to buy upgrades with the day's coins: second kettle, bigger tray
(carry 3 → 5 items), faster oven, an apprentice who clears tables, an extra table, comfier stools
(+patience). A star rating per day (1-3) from coins and happy customers. Victory after day 7, then an
endless shift. Best score saved in localStorage.

INTERACTION: point-and-click: click a station to start or collect an item (it goes on your tray), click
a table to serve what it needs from the tray or to clear glasses. The waiter character walks to each
target along the shortest path (queue several clicks). Drag to pan a little, scroll to zoom (clamped).
Keyboard shortcuts for stations (1-4), Space to pause.

HUD: frosted glass panels over the 3D view: top bar (logo, day, clock for the day, coins vs target,
happy / angry counters, speed, pause, sound, language), the tray contents with item icons, order bubbles
and patience rings above customers, floating "+coins" numbers, short toasts, title, day-end summary,
shop, victory and game-over screens. Turkish by default with an English toggle.

EXTRAS: all sound effects synthesised with Web Audio (kettle whistle, spoon clink, cash register,
angry grumble, door bell, gulls, a light street ambience), mute toggle; works on mobile with touch.

PERFORMANCE (must run at 60 fps on an Intel UHD 620 laptop iGPU): renderer pixel ratio capped at 1.5,
antialias on, one directional light with a 1024 shadow map that only covers the play area, InstancedMesh
for repeated objects, merged static geometry with vertex colours, MeshLambert materials only, no
post-processing, at most ~150 draw calls, particles as one Points object. Pause the simulation when the
tab is hidden. Add a "?low" URL flag that turns shadows off and halves particles.

SELF-TEST: add a "?auto" URL flag that plays the game by itself (takes orders, prepares and serves,
clears tables, buys upgrades, runs at 3x) and a "?debug" flag that shows draw calls on screen and exposes
window.<game>Sim(seconds) to fast-forward the simulation without rendering, so the game can be verified
in a real browser without a human. Expose the live game state on window.<game>.

Deliver only the finished index.html. No build step, no server needed: it must open by double-clicking
the file.
```
