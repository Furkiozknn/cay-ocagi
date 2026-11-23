# Credits

The 3D models, icons, logo and cobblestone texture were generated for this game (Higgsfield Tripo text-to-3D and GPT
Image, processed with glTF-Transform); prompts and sizes are in [docs/assets.md](docs/assets.md). Every other part of the
game world and every sound is generated in code.

| File | What | Source | Licence |
|---|---|---|---|
| `assets/mascot/kedi-uyuyor.png` | White Turkish Van kitten asleep on a cushion (shop screen and day summary, a 2D corner image only), 340 px | Furkiozknn's own Claude Game Arena mascot (`video-studio/public/maskot/kedi-uyuyor.png`), copied unchanged (already below 512 px / 150 KB) | Owned by the project author, used with permission |

The game runs without this file: `?noassets` skips it, and a missing file simply hides the kitten.

## Facts on the day cards

The six "Did you know?" cards (day briefing and day summary) use only these sources (the URL of each fact is also next to
it in `index.html`, table `FACTS`):

- UNESCO inscribed tea culture (joint file with Azerbaijan) in 2022: https://ich.unesco.org/en/RL/01685
- Rize tea farming since 1924, "Rize Çayı" geographical indication in 2022; about 830,000 decares and 1.35 million tonnes of fresh leaf per year: https://www.ntv.com.tr/galeri/n-life/n-lezzetler/rize-cayina-98-yil-sonra-tescil,v3ZWxvy0OUS_XSPfFi1s7w
- 2025 fresh tea processing, ÇAYKUR and private-sector shares: https://www.rtb.org.tr/uploads/files/110-Çay_Sektörü_Raporu_Ocak_2026.doc
- Simit standardised in Üsküdar in 1593; Evliya Çelebi counted 70 simit bakeries in the 1630s: https://en.wikipedia.org/wiki/Simit

## 3D style pass (stil-3d, November 2026)

The 2D parts were moved to the stylised 3D chibi look of the game's song video. Everything new is in `assets/3d/`
(webp); the old `assets/ui/` files stay for the README and `?noassets`.

| File | What | Tool |
|---|---|---|
| `assets/3d/kapak.webp`, `kapak_bulanik.webp` | Title screen backdrop: the song video's key frame (tea waiter and tourist at the quay tea house), plus a 96 px copy for the blurred side fill | Frame from the song reel (`atolye/muzik-reel/oyunlar/cay-ocagi/gorsel/genis.jpg`, Flow · Nano Banana Pro), reused unchanged |
| `assets/3d/ikon/tea, simit, coffee` | Item icons | Adobe Firefly (Gemini 3.1 · Nano Banana 2 model) with the video frame as reference image, one 2x2 sheet, sliced and keyed locally (PIL) |
| `assets/3d/ikon/kettle, oven, sink, dirty, coin, star, stool, table, tray, happy, angry, group, office, ayran, logo` | Station, shop, HUD icons and the title logo | Adobe Firefly (same model and reference), one 4x4 sheet, sliced and keyed locally |
| `assets/3d/ikon/regular, apprentice, person`, `assets/3d/portre/*.webp` | The seven named regulars' portraits and the customer / apprentice icons | Adobe Firefly (same model and reference), one 3x3 sheet, sliced and keyed locally |

The waiter, apprentice and customers in the game world are now chibi figures built in code (big round heads, dot eyes,
rosy cheeks, the waiter's moustache, brown apron and brass hanging tray, after the video); materials use smooth shading
and the generated models get smoothed normals.
