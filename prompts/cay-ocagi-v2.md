# Çay Ocağı v2 upgrade brief — for Claude Sonnet 5.5

## What the game is now
A 3D time-management game on an Istanbul quay: a waiter (click-to-queue actions) brews tea in the semaver, warms simit, cooks Turkish coffee (must be grabbed within 3.6 s), pours ayran and washes glasses, serving 3 to 6 tables.
Seven days (`DAYS`, targets 65 to 250 coins, a new item or customer type each day), a shop between days (`UPG`), 1-3 stars (`ratingStars`) and an endless shift after day 7 (`dayCfg`).
Customers (`CUST`: normal, office, group, regular) arrive on a fixed gap (`spawnUpdate`) with a patience ring. Remastered models and icons, a ferry crossing the strait (`ambientUpdate`), gulls, a ginger cat you can pet.
The `?auto` waiter wins 84 of 90 seeded games. The look is already strong (screenshot busy_d6_en.png); the weak spots are the entry flow, a flat arrival rhythm and shallow tea and table mechanics.

## Upgrade items (priority order)

### 1. Straight into day 1, with coach marks (first 30 seconds)
- Why: Play runs `A.start()` which goes to the day briefing (`A.intro`), then a second click starts the day; the only teaching is one toast ("Click the tea station (1) first", `beginDay`). Also `toast()` (line ~2041) does not deduplicate: the busy-day screenshot shows "Coffee is ready, grab it!" stacked twice.
- What to build: on a fresh save Play starts day 1 directly (the briefing becomes a top banner for 3 s). Three pulsing coach marks that advance on the real action: tea station, then the first customer's bubble, then the table. Later days keep the briefing. Deduplicate toasts by key (same message within 2.4 s refreshes instead of stacking).
- Done-when: 1 click from page load to a running day; coach marks show once; `?auto` and `?bench` unaffected.

### 2. The ferry rush (mechanic depth, from research: simit-tea-ferry culture)
- Why: arrivals are a constant `gap` (e.g. 6.0 s on day 5), so there is no rhythm and no reason to prepare; yet the strait ferry already crosses with a horn (`ambientUpdate`, `ferry.horn`).
- What to build: when the ferry reaches the quay, a "ferry rush" fills 2 to 3 parties (as many as free tables allow) within 10 s, then a quiet gap. Make the horn the warning: a banner "Vapur geliyor / Ferry arriving" 8 s before, so the player pre-brews tea and warms simit. Keep the day's expected customer count the same (shift the rest of the gaps, do not add customers), starting from day 3.
- Done-when: `window.cay` exposes `rushes`; bot still wins at least 80 of 90 in `?bench`; targets in `DAYS` unchanged.

### 3. Tea freshness and the tray (mechanic depth, from research: semaver, thin-waisted glass)
- Why: tea is one 7 s brew that gives 4 glasses forever (`BREW_T`, `TEA_N`), so tea never requires attention after day 1.
- What to build: poured tea has a freshness bar that falls after 25 s on the tray or counter; stale tea is still accepted but pays half the tip and plays a grumble (`sfxGrumble`). A refreshed pour is shown by steam (reuse the particles). Keep the numbers inside the existing data tables so the bot reads them.
- Done-when: debug shows `fresh` per glass; stale tea is visibly greyer; bot wins at least 80 of 90.

### 4. Refills and the regulars' tavla (mechanic depth, from research: tavla, okey and kiraathane culture)
- Why: a party eats once (`eat` time) and leaves, so a table never creates a second decision; regulars are only a double-tip skin (`CUST.regular`).
- What to build: regulars sit and play tavla: they stay 2.5x longer, show a dice icon, and every 14 to 18 s ask for a tea refill ("bir çay daha") with a fresh patience ring; each fulfilled refill adds tip and a small combo. A new table state in `partiesUpdate`, shown in the existing bubble system.
- Done-when: a debug spawn of a regular produces at least 2 refill orders; the day-7 target stays reachable by the bot.

### 5. Gull thieves
- Why: the gulls only circle (`gulls`, `ambientUpdate`); the street has no threat or feedback, and gulls are a core Istanbul simit story.
- What to build: from day 3 a gull occasionally lands on the quay rail, then dives at a served simit left on a table; click or tap it to shoo it away (squawk, feathers, coins unaffected); if it succeeds the simit's coins are lost. Max one gull at a time, never during the first 20 s of a day.
- Done-when: bot shoos gulls (same click action); gull events appear in `window.cay.log`.

### 6. Day cards with facts and the apprentice rank
- Why: the day briefing shows only the menu and target; no learning layer.
- What to build: a fact card on each day briefing and on the day summary (one `FACTS` table, source URL per entry in a comment). Add a rank line on the summary from lifetime coins: Çırak, Kalfa, Usta (apprentice, journeyman, master), purely cosmetic, in a new `arena.cay.best` field.
- Done-when: both languages; ranks persist; stars and day count unchanged.

### 7. Order urgency feedback
- Why: the patience ring is the only urgency cue and the ready-coffee window (`COOK_WIN`) has no visual timer.
- What to build: the order bubble shakes and reddens in the last 25 % of patience; the coffee station shows a shrinking ring during the 3.6 s window; a short bell on a perfect pick-up (inside the first 1 s of the window) with a small tip bonus included in the existing tip data.
- Done-when: visible in screenshots; fps and draw calls within the round-6 budget (31 draw calls at the moment).

## Facts for in-game cards
1. TR: UNESCO, çay kültürünü 2022'de Azerbaycan ile ortak dosyayla Somut Olmayan Kültürel Miras Temsili Listesi'ne aldı. EN: In 2022 UNESCO inscribed tea culture, a joint file with Azerbaijan, on its Representative List of the Intangible Cultural Heritage. Source: https://ich.unesco.org/en/RL/01685
2. TR: Rize'de çay tarımı 1924'te başladı; "Rize Çayı" 2022'de coğrafi işaret aldı. EN: Tea farming in Rize began in 1924, and "Rize Çayı" received a geographical indication in 2022. Source: https://www.ntv.com.tr/galeri/n-life/n-lezzetler/rize-cayina-98-yil-sonra-tescil,v3ZWxvy0OUS_XSPfFi1s7w
3. TR: Çay yaklaşık 830 bin dekarda yetişiyor; yıllık yaş çay üretimi yaklaşık 1,35 milyon ton. EN: Tea grows on about 830,000 decares, and the annual fresh-leaf harvest is about 1.35 million tonnes. Source: https://www.ntv.com.tr/galeri/n-life/n-lezzetler/rize-cayina-98-yil-sonra-tescil,v3ZWxvy0OUS_XSPfFi1s7w
4. TR: 2025'te 1.338.662.452 kg yaş çay işlendi; %61,46'sı ÇAYKUR'da, %38,54'ü özel sektörde. EN: In 2025, 1,338,662,452 kg of fresh tea leaf was processed: 61.46% by ÇAYKUR and 38.54% by the private sector. Source: https://www.rtb.org.tr/uploads/files/110-Çay_Sektörü_Raporu_Ocak_2026.doc
5. TR: 1593'te Üsküdar'da simidin ağırlığı ve fiyatı standartlaştırıldı. EN: In 1593, the weight and price of simit were standardised in Üsküdar. Source: https://en.wikipedia.org/wiki/Simit
6. TR: Evliya Çelebi 1630'larda İstanbul'da 70 simit fırını saydı. EN: In the 1630s Evliya Çelebi counted 70 simit bakeries in Istanbul. Source: https://en.wikipedia.org/wiki/Simit

## Mascot
Yes, small: the white Turkish Van kitten asleep on a cushion beside the semaver icon on the shop screen and next to the stars on the day summary (a 2D corner image only, `assets/mascot/`). The ginger quay cat stays as it is.

## Do not break
- Play first; 7 days, `DAYS` targets, the stars rule (1 / 125 % + 70 % happy / 150 % + 88 % happy), the 3-minute day, endless after day 7.
- Flags `?auto ?debug ?sim ?bench ?low ?noassets ?nosave ?day ?lang ?seed`, `window.cay`, `window.caySim`, `window.cayBench`.
- Keys `arena.cay.settings|save|best` (add fields only; the save keeps `v: 1`), keys 1-5 for stations, queue of six clicks, pause, mute, speed 1x/2x/3x.
- The failed-day-keeps-coins rule and the shop upgrade tables.

The shared rules in `_sablonlar/arena-upgrade-common.md` (and `_sablonlar/arena-round6-common.md`) are appended to this brief.
