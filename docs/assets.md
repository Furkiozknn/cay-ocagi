# Asset remaster

Çay Ocağı was first built with every model, icon and texture made in code. This remaster replaces the weakest
procedural pieces with generated assets, keeps every procedural builder as a fallback, and does not change game rules
or balance.

- **3D models:** Higgsfield `tripo_3d` (text-to-3D, standard texture, `pbr: false`, `face_limit` 1000-2000), then
  glTF-Transform: `center --pivot below`, `resize` (512 px for the five stations and the ferry, 256 px for the rest),
  `webp`. Orientation, size and anchor points are set in code (`MODEL_YAW`, `applyAssets()`, `FIT`).
- **2D:** Higgsfield `gpt_image_2_5` (quality high): one 5×4 icon sheet (transparent) sliced with sharp into 20 PNGs
  (`claude-game-arena/tools/assets/slice-cay.cjs`), one logo badge, one tileable cobblestone texture.
- **Loading:** the 20 icon PNGs and the logo are preloaded with `new Image()` (also from `file://`) and only replace
  the drawn icons when every file decodes. `GLTFLoader` (`await import('three/addons/loaders/GLTFLoader.js')`) at start-up while the title screen
  shows "Loading… n/N" (31 files: 21 images, 9 models, 1 texture) and the Play button is disabled. Each model that loads replaces its procedural part (the merged
  world mesh is rebuilt without it); anything that fails keeps the original. From `file://` the loader is skipped (Chrome
  blocks fetch there) and the original 3D look is used; `?noassets` forces the full original look over http, icons
  included. `?auto` starts at once as before and the models swap in when they arrive (after 15 s at most).
- Shared negative prompt for models: `realistic, photoreal, base, pedestal, ground` (+ `table`, `people`, `water`, … where
  noted). Shared prompt suffix: `toy-like clay look, flat simple colours, chunky proportions`.
- Measuring helpers added to `claude-game-arena/tools/assets`: `front.mjs` (front depth map, used to find the oven's
  mouth), `top.mjs` (top-down height map), `pots.mjs` (finds raised parts such as the cezve pots after a yaw),
  `fps-cay.mjs` (fps / draw calls / bot log of this game in headless Chrome), `shot.ps1` (screenshot that waits for
  Chrome and kills only its own processes).

## Done

| Asset | Category | Replaced (why) | Tool / prompt | File | Format / size / tris | QC | Integrated | Remaining issues |
|---|---|---|---|---|---|---|---|---|
| semaver | station (tea) | stacked bronze cylinders | tripo_3d: "Low-poly stylized Turkish brass samovar game asset, tall polished bronze tea urn with a small tap on the front, white porcelain teapot with red lid sitting on the top, …" (neg. + table) | `assets/models/semaver.glb` | GLB, 512 px WebP, 75 KB, 1,386 tris | good; tap already faces +X; 2.0 m tall on the counter, texture brightened ×1.2 | yes (also the second, toggleable semaver `DYN.kettleB`) | a small brown lump next to the teapot on the lid |
| oven | station (simit) | brown box + chimney cylinder | tripo_3d: "Low-poly stylized small brick bread oven game asset, boxy terracotta brick oven with an arched glowing orange opening on the front, a short stone chimney on the back corner, flat roof slab, …" (neg. + table, people) | `assets/models/oven.glb` | GLB, 512 px, 62 KB, 1,298 tris | good; mouth faced +Z, turned +90°; glow plane moved to the back of the arched mouth, smoke emitter found on the chimney, ready simits on the roof slab | yes | – |
| coffee | station (Turkish coffee) | sand tray + cylinder pots | tripo_3d: "Low-poly stylized Turkish coffee sand stove game asset, wide shallow wooden box filled with golden sand, two small copper cezve coffee pots with long handles standing in the sand, a dark iron burner panel at the back, …" | `assets/models/coffee.glb` | GLB, 512 px, 75 KB, 1,410 tris | good; generated at 45°, turned +45° so the panel is against the wall; foam and steam anchored on the two pot mouths | yes | a third small pot in the sand |
| ayran | station (ayran) | white box with cylinders | tripo_3d: "Low-poly stylized small drinks cooler fridge game asset, boxy white and light blue refrigerator with a glass front door showing rows of white ayran cups, blue header panel on top, …" (neg. + text) | `assets/models/ayran.glb` | GLB, 512 px, 90 KB, 1,398 tris | good; door already faces +X; tall and narrow, so it is widened 1.4× in x/z to fill the old 1.5 m footprint | yes | – |
| sink | station (sink) | box basin + tap | tripo_3d: "Low-poly stylized kitchen sink cabinet game asset, light blue-grey cabinet with a steel basin of blue water, a curved chrome tap, a small drying rack with a few upside-down tea glasses beside the basin, …" | `assets/models/sink.glb` | GLB, 512 px, 83 KB, 1,218 tris | good; turned +90° (long side along the counter) | yes | the drying-rack glasses are small blobs |
| table | tables (6 instances) | cylinder top + leg | tripo_3d: "Low-poly stylized round cafe table game asset, small round bistro table covered with a plain white tablecloth hanging down at the edges, one dark iron pedestal leg with a round foot, …" (neg. + chairs, cups, plates, people) | `assets/models/table.glb` | GLB, 256 px, 45 KB, 899 tris | good; the white cloth takes the existing per-table instance colours; top kept at the old height (y 0.975) so served items sit on it | yes (one InstancedMesh, replaces 2) | a thin stray thread under the cloth on one side |
| window | façade (5 instances) | dark box + shutters + sphere flowers | tripo_3d: "Low-poly stylized Istanbul house window game asset, a single tall rectangular window with a cream stone frame, two open teal wooden shutters on the sides, a wooden flower box below full of red and pink geraniums, flat back side to mount on a wall, …" (neg. + wall, building) | `assets/models/window.glb` | GLB, 256 px, 58 KB, 882 tris | good; faces +X, back flush with the façade, 1.05 m tall under the roof slab | yes (one InstancedMesh) | – |
| ferry | scenery | box hull + cabins | tripo_3d: "Low-poly stylized Istanbul Bosphorus passenger ferry vapur game asset, long white hull with a dark blue stripe, two-deck white cabin with rows of dark windows, one tall orange-red funnel with a black top, pointed bow, …" (neg. + water, people), face_limit 2000 | `assets/models/ferry.glb` | GLB, 512 px, 116 KB, 1,822 tris | good; bow faced +Z, turned +90°; 8 m long, sunk 0.55 m into the water | yes | – |
| cat | ambient | spheres + cones | tripo_3d: "Low-poly stylized ginger street cat game asset, chubby orange tabby cat with white paws and white chest, standing on four legs, tail up, side view, …" (neg. + fur detail) | `assets/models/cat.glb` | GLB, 256 px, 48 KB, 948 tris | good; already faces +X like the old cat; the procedural bob / sleep squash still runs on it | yes | the sleeping pose is the standing model squashed |
| cobblestone | ground texture | 713 flat quads in random tones | gpt_image_2_5 1k: "Seamless tileable top-down texture of an old Istanbul cobblestone street, rounded warm beige, sand and light grey stones … soft painted stylized game texture with a toy clay look, flat even lighting …" | `assets/textures/cobblestone.webp` | WebP 512 px, 34 KB | good; tiles cleanly; contrast and saturation lowered and pulled to the old ground tone (#cebfa6) so the UI and characters stay readable; one tile per 5 m | yes (one textured plane, 2 triangles) | – |
| icon sheet | UI | inline SVG icons (the "second samovar" icon read as a handbag) | gpt_image_2_5 2k transparent 5:4: "Game UI icon sheet: exactly 20 separate icons arranged in a strict grid of 5 columns and 4 rows … chunky stylized cosy cafe game icons, soft painted clay look, thick dark brown outline, warm palette of terracotta orange #C2410C, cream, amber, teal and gold …" (tea glass, simit, coffee cup, ayran cup, sink, dirty glass, coin, happy, angry, star, customer, office worker, group, regular, samovar, tray, oven, apprentice, table, stool) | `assets/ui/icons/*.png` | 20 × PNG 96 px, 5-8 KB each | good; checked at 48 and 24 px on the game's cream; glow removed (alpha < 70 → 0), specks dropped | yes (top bar, dock, tray, order bubbles, station badges, intro menu, shop, summary stars; the empty star is the gold star greyed) | – |
| logo | UI | tulip SVG + text | gpt_image_2_5 1k transparent: "Game logo emblem for a cosy Istanbul tea house game, no text … a round badge … a big Turkish tulip-shaped tea glass full of glowing red-amber tea on a small white saucer with curls of steam, behind it a small cream silhouette of a mosque dome and two minarets and a little ferry on blue water …" | `assets/ui/logo.png`, `assets/ui/favicon.png` | PNG 256 px (32 KB), 64 px favicon | good | yes (title card, favicon; the top bar uses the tea icon since the overnight pass, the logo was unreadable at 26 px) | – |

Also changed in code, no credits:

- **Served and carried items:** every item used to be the same small cylinder recoloured. Now each type has its own
  small vertex-coloured model built in code (tulip tea glass on a saucer, simit on a plate, coffee cup on a saucer,
  ayran cup with a blue band, an empty used glass and a plate with crumbs), one InstancedMesh per type shared by the
  waiter's tray, the tables and the apprentice (`ITM`, 60-150 triangles each; dirty coffee and ayran cups are tinted).
  These are used in the `file://` / `?noassets` fallback too.
- The game-over retry button was labelled "Shop"; it now reads "Retry the day" / "Günü tekrar dene".

## Failed / rejected

| Asset | Tool / prompt | Why rejected |
|---|---|---|
| tea-glass | tripo_3d: "Low-poly stylized Turkish tea glass game asset, small tulip-shaped glass full of deep red-amber tea standing on a small white saucer, …" (5 credits) | muddy brown, lumpy, and tripo's minimum face limit is 1,000 (asked for 400): too heavy for up to 44 instances per item type. Replaced by the small models built in code (see above). Not integrated, file not in the repo. |

## Not started

- Tea house façade as one model: the gameplay positions (counter top, station slots, sign) are hard-coded, so parts
  were replaced instead (stations, windows). The awning, counter, wall shelves and door stay procedural.
- Customers, strollers, waiter and apprentice: kept procedural (40 tinted instances × 4 parts).
- Stools, geranium pots, lamp posts, bunting, gulls, waves, sailboats, far shore: procedural is fine at this size.
- Sea texture: the flat sea with moving wave strips already reads well.
- Audio: no generation model available for standalone sound; the synthesised Web Audio sounds stay.

## Credits

Higgsfield balance 243.53 → 187.78: **55.75 credits** of the 80-credit budget (11 × tripo_3d at 5, icon sheet 2.75,
logo 1.5, cobblestone texture 1.5).

Overnight pass (2026-10-21): no new assets were generated (0 credits); the review listed no missing asset for the
HTML version, and the top-bar mark problem was solved with the existing tea icon. Balance seen at the start: 170.28.

## Verification

Measured on 2026-10-20 (overnight pass) on the real GPU (ANGLE, Intel UHD Graphics, i5-10210U) in headless Chrome at
1280×720 with `tools/assets`-style DevTools scripts; the earlier unverified numbers were replaced by these.

| Check | Before the overnight pass (remaster only) | After |
|---|---|---|
| `?auto&debug&sim=900` (busy day 6), fps over 4 s | 60.1 fps, 31 draw calls (max 31), 50.4 k triangles | 60.1 fps, 31 draw calls (max 34), 49.9 k triangles |
| same with `?low` | 60.1 fps, 30 calls | 60.1 fps, 31 calls |
| same with `?noassets` | 60.2 fps, 27 calls, 38.6 k triangles | 60.1 fps, 26 calls, 38.0 k triangles |
| `?auto&bench=12` | 12/12 wins | 11/12 wins |
| `cayBench(30, base)` for base 2, 3, 4 (90 games) | 82/90 wins | 84/90 wins |
| `?auto&debug&sim=2600` (bot plays on) | victory, endless to day 9, 0 errors | victory, endless to day 9, 0 errors |
| `?auto&debug` real time, 60 s | n/a | 0 errors |
| `file://` (`?auto&debug&sim=300`) | n/a | procedural models, icon PNGs, 0 errors, no console messages |
| `?noassets` | n/a | drawn icons, no logo image, Play enabled at once, 0 errors |
| Title card re-renders while loading | about 10 (one per asset) | 2 (first paint and the final one) |

The bench is deterministic per build, but any change in behaviour shifts the random sequence, so single 12-game runs
move by one or two wins; the 90-game comparison is the meaningful one.

## Notes for regenerating models

The oven glow box (`DYN.ovenGlow` in `applyAssets()`) and the coffee pot anchors (`FIT.pots`) use offsets measured in
the raw units of these exact GLBs (raw height about 1). If `oven.glb` or `coffee.glb` is regenerated, measure them again
with `tools/assets/front.mjs` and `tools/assets/pots.mjs`; nothing checks them at run time.
