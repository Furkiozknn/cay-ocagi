# 🍵 Çay Ocağı · Tea House Rush

**[▶ Play in your browser](https://furkiozknn.github.io/cay-ocagi/)** · Türkçe & English · desktop & mobile · free, no install

A low-poly 3D time-management game in a **single `index.html`** (Three.js, no build step, no asset files).
Run a tiny tea house on an Istanbul quay for seven busy days: brew tea, warm simit, cook Turkish coffee
without letting it boil over, serve every table before patience runs out, and keep the glasses washed.

![A busy day 6 at the tea house](docs/busy_d6_en.png)

## Features

- **Four menu items, each with its own station:** tea has to brew in the semaver, simit warms in the oven,
  Turkish coffee must be grabbed in time or it boils over, ayran is instant. Dirty glasses go to the sink.
- **Seven days, each adding something new:** a new menu item, a hurried office worker with little patience,
  a group of three, a regular who tips double. Coin target and 1-3 stars per day, then an endless shift.
- **Shop between days:** second semaver, bigger tray (3 → 5 items), faster oven, an apprentice who clears tables,
  extra tables, comfy stools (+20% patience).
- **Tips depend on speed.** Customers who wait too long leave angry; customers who find no free table walk by.
- **A living street:** a ferry crossing the strait, seagulls, lamp posts, bunting, geraniums and a cat you can pet.
- **Everything is generated in code:** the scene, the characters and every sound effect (Web Audio).
- **Light on the GPU:** about 25 draw calls, instanced meshes, one 1024 shadow map. Use `?low` if it stutters.

| | |
|---|---|
| ![Counter close-up](docs/zoom1.png) | ![Shop](docs/shop_en.png) |
| ![Day summary](docs/summary_en.png) | ![Phone layout](docs/mobile_final.png) |

## Controls

| Action | Mouse / keyboard | Touch |
|---|---|---|
| Use a station (brew, collect, wash) | Click it, or keys `1`-`5` | Tap the station or its button |
| Serve or clear a table | Click the table or its order bubble | Tap |
| Drop an item from the tray | Click the tray slot | Tap |
| Clear the walking queue | `Esc` or right-click | — |
| Pan / zoom | Drag / mouse wheel | Drag / pinch |
| Pause | `Space` | Pause button |

The waiter walks to each target in order; you can queue up to six clicks.

## Run it locally

Download the repo and double-click `index.html` (it needs internet the first time, to load Three.js and the font from a CDN),
or serve the folder with `python -m http.server 8766` and open http://127.0.0.1:8766/.

### URL flags

| Flag | Effect |
|---|---|
| `?low` | No shadows, half the particles |
| `?lang=en` / `?lang=tr` | Interface language |
| `?auto` | The game plays itself at 3× speed (self-test bot) |
| `?debug` | Draw calls, fps and any JS errors on screen; `window.caySim(seconds)` fast-forwards the simulation |
| `?sim=N` | Fast-forwards N seconds before the first frame |
| `?bench=N` | Runs N full bot games and prints per-day results |

## How it was made

Game #2 of a series in which **every game is built by a different AI model**, to compare which one makes the better game.
This one was built by **Claude Sonnet 5.5** (`claude-sonnet-5-5`) with [Claude Code](https://claude.com/claude-code), on its own,
from one prompt: [`prompts/cay-ocagi.md`](prompts/cay-ocagi.md). Every model gets the same prompt structure, quality bar and
self-test rule, and none of them sees the other games' code. The orchestrating session (Claude Opus 5.5) wrote the prompt,
re-tested the result independently and published it.

Verification before release: the self-test bot played 30 full games (26 wins; it is pushed on days 5 and 7, while a deliberately
slow bot loses around days 3-5), an independent run reached the endless shift with zero JS errors, plus screenshot checks of every
screen in Turkish and English and a 390 px phone layout.

| # | Game | Genre | Model |
|---|---|---|---|
| 1 | [Hisar](https://github.com/Furkiozknn/kale-savunmasi) | Tower defense, 3D | Claude Opus 5.5 |
| 2 | **Çay Ocağı** | Time management, 3D | Claude Sonnet 5.5 |
| 3 | [Çini Ustası](https://github.com/Furkiozknn/cini-ustasi) | Puzzle, 3D | Claude Fable 5.1 |

## Tech

Three.js r170 (ES module from jsDelivr), Web Audio API, one Google Font. About 2,000 lines of plain JavaScript, HTML and CSS in one file.

## License

Code: [MIT](LICENSE).
