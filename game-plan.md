# Game Plan — "beat the game 7"

Goal: beat the Ender Dragon.

## Current state (≈day 6, thunderstorm, paused in PauseScreen)

- Position: `x≈69.3, y=69, z≈-61.5` — open plains a few m from the crafting table, yaw 160. Thunderstorm (dark, mobs may spawn; thunder heard within 40 m).
- Health 20, hunger 20, **saturation 17 and draining** — first food warning. No food stock.
- Hotbar: 1 sapling, wooden axe (46/59), stone pickaxe (~110/131), 15 dirt, 6 cobble, furnace, wheat seeds, stone axe (full), stone sword (131/131 — **has never landed a hit**). Inventory: 3 sticks (old note).
- **No wood, no wool, no food** after 2 in-game days — reset priorities below.
- Advancements: Stone Age, Getting an Upgrade.

## Landmarks (verified this session unless noted)

- **Crafting table**: `(67, -67)` on the y≈69 plain, by the lake side (`x<55` = water).
- **Ledge oak**: big oak on the terrace ledge ABOVE the table plain — bearing ~55 from the table, ~5 m, trunk base y≈72–73. Not reachable at eye level from the plain; must climb/pillar the ledge. (Canopy visible from the table.)
- **Sheep-herd hills**: `(110–165, -80..-100)` — multiple sheep (heard from many bearings at once), donkey, cows, chickens; steep terraces, tall grass. Terrible melee terrain.
- **"Zombie Horse"**: undead-sounding mob stalking the herd area; cried all night, burned ("hurts") at sunrise. Hostile or neutral — unknown. Avoid or fight with reach.
- **Gorge**: `z≈-105..-114`, drowning trap (underwater overhangs), cliff on south bank. Skirted at z≈-104 — safe side.
- **Cave mouths**: one under the old tunnel `(174, -71)`; one seen ~(116, -104) area from a hilltop (unverified).
- **Dark forest**: old note `(75–82, -40..-58)` **NOT verified** — walked through it in storm-dark, saw no trees. Re-check in daylight.
- **Ruined portal**: old note `x≈100+, z≈-60` — unlooted, unverified this session.

## Hard-won lessons

### The Latency Law (cost me 2 days)
- **Every tool call burns 100–800 ticks (5–40 s) of game time.** runAlias state diffs are captured BEFORE execution → pos/yaw in the response is STALE. Always `getState` after movement before computing bearings.
- Sound events carry their emission tick. Acting on one costs ≥1 call → **every sound fix is 5–20 s stale = 5–20 m of mob wander error.** Chasing sheep by baah bearings got 0 hits in ~15000 ticks.
- Sound-bearing rule (verified): bearing = current yaw + offset; positive offset = screen-RIGHT. Screen-LEFT = negative offset (misread once, walked 40 m wrong way).
- Therefore: hunt only stationary/confirmed targets. **"Block broken" chomp = grazing sheep, stationary ~5 s.** Approach during the graze; on arrival `sweep_attack` (cfg) — ±35° yaw sweep, then **verify via durability delta** (unchanged = all whiffed). Better: **pit-trap** — sheep twice walked to my hole rim; a 1×1×3 pit near grazing spots catches wanderers, making them static targets.
- Sword durability = hit detector. Tall grass eats attacks (no durability loss, "Block broken" sounds) — aim at body height over grass tops or from the same level.

### Controls / physics
- **Pitch: −90 = UP, +90 = DOWN** (vanilla). Dig with 90, un-cap with −90. (I lost ~10 min staring at the sky trying to dig.)
- **Digging straight down while standing near a block edge mines the NEIGHBOR column** — blocks break, durability drops, but you don't fall. Check x/z fraction ≈ .5; else walk into the hole column first, then continue.
- `+loop_tower` (jump + place beneath) = pillar up/out. `+hop` overshoots targets — stop hops 5+ m short and walk.
- Breaking the block under you: y drops only when the ray column == support column (see edge rule).

### Hazard signs — "could fall in a 1×1 hole" / "blocked by full block" (game-logic derived)
- Sensor model: eye 1.62 m, block reach 4.5 m, hitbox 0.6×1.8, auto-step ≤0.6, jump 1.25, sprint ≈5.6 m/s (~11–13 m per 40-tick hop leg), fall damage from the 4th block (`floor(fall−3)`).
- **Floor radar**: at pitch θ down, flat floor reads `target.distance = 1.62/sinθ` → 30°=3.24, 45°=2.29, 60°=1.87, 90°=1.62. (Verified at 4 points by the accidental pit: 1.62 solid / 2.62 1-deep / 3.62 2-deep / `null` at 5.62 > reach.)
  - **Distance JUMP +1.4 ≈ 1-deep hole 1 block ahead; jump to `null` = ≥2-deep gap.** Distance DROP = rising ground/step.
- **Walls**: a ≥2-high wall shows at pitch 0 as full-block `target` ≤1.4 when adjacent. **A 1-high knee-blocker is INVISIBLE at pitch 0** (1.62 m eye-ray passes over it) — only caught at pitch 30–45 as a distance drop (2.29 → ~0.9 adjacent).
- **Motion-result signs** (movement key held): x/z frozen + y constant = blocked by full block; y oscillating ±1.25 = jumping against a ≥2-high wall (hop can't mount it); y +0.5 without jump input = auto-stepped slab/stairs; y stops rising while towering = ceiling above; `feet` leaves `on_ground` + y dropping = stepped into a hole (drop ~1 then ground = jump out; continuous drop = ravine).
- `null` at pitch 90 while `on_ground` = edge-column mismatch (don't dig there — re-center frac ≈ .5).
- **Travel stance**: open ground → pitch 30–45 (floor radar); caves/forests/combat → pitch 0. A hop leg with displacement ≪11 m = collision happened.

### Coordinate signs — hitbox ±0.3 boundary rules (derived on hole-checkerboard)
- Footprint = center ±0.3 per axis; you stand iff a solid top overlaps it. `frac(c) = c mod 1`:
  - **frac ∈ [0.3, 0.7]** → footprint inside ONE column → support = that column alone (over a hole = you fall).
  - **frac < 0.3 or > 0.7** → straddles two columns → support = OR of both (fall only if BOTH are holes).
- **Falling**: axis-aligned on a checkerboard row, you fall when frac(travel axis) enters [0.3, 0.7] of a hole column; warning = frac approaching 0.3 (+dir) / 0.7 (−dir). **Immunity: straddle the off-axis boundary (frac outside [0.3,0.7]) → footprint covers a 2×2 with 2 diagonal solids → 1×1 holes can't catch you.** Hug boundaries / walk slightly diagonal on holey ground.
- **Blocked**: wall face = integer boundary → center pins at **frac 0.70 (moving +) / 0.30 (moving −)**, axis displacement ≈0 with key held. Pinned frac tells wall side without looking; both axes pinned = cornered. Fell-in-hole + face-planted = y one low + axis pinned at .70/.30 → jump out (1.25 > 1, `+hop` does it).
- Same 0.3 (half hitbox) drives both signs: fall window [0.3,0.7] and wall pins 0.70/0.30 are complements. Strafe nudge ≈2–3 ticks ≈ 0.2 m to enter/leave a window.

### Night hole (2 nights, proven)
- Dig 3 deep (feet = rim−3), cap = place dirt against the **inner wall face one layer below the rim** from inside (placement at head layer is blocked by self-collision; one below works, 2-block headroom, no suffocation).
- Verify seal: pitch −90 screenshot = pitch black. Exit: break cap, `+loop_tower` ~34 ticks, hop off.
- Costs ~1.5 hunger shanks to dig out; negligible to wait.
- **Cap at surface level gets grass spread in daylight** → target reads "Grass Block" = free morning detector.

### Time & weather
- **The tick counter ≠ world clock.** Never compute time of day from ticks. Use sky screenshots (or grass-spread cap).
- Yaw mapping verified by movement AND sunrise: **0=+z(south), 90=−x(west), 180=−z(north), −90=+x(east)**; sun rises at yaw ≈ −90/270.
- Thunderstorm = night-dark + daytime mob spawns + awful visibility. Don't screenshot-navigate in rain; use `target` block names. Treat storms like night: hole up or do target-guided work only.

### Older lessons (still valid)
- Chop trunks POINT-BLANK (≤1 m) or drops fling out of reach. Stand at trunk base, break eye-level log, then look up/down for the rest — drops fall on you.
- `applyRecipe` only stages the grid — take the result from slot `c1` (both 2×2 and 3×3).
- Axe `tap_use` on a log STRIPS it — don't hold an axe when opening/placing.
- Rivers/gorge: underwater overhangs freeze you while breath drops — back out horizontally while jumping. Underwater mining ~5× slower.
- `+loop_tower` fails if blocked above.

## Next actions (priority order)

1. **WOOD (critical path)**: from the table (67,−67), face ~55, pillar 3 dirt up the ledge (`+loop_tower`), scan `target` at eye level for Oak Log, chop point-blank (wooden axe first, then stone axe). Goal ≥8 logs. If ledge oak fails in DAYLIGHT: re-verify dark forest at (75–82, −40..−58).
2. **CHARCOAL + TORCHES**: place furnace, burn logs→charcoal (fuel: planks), craft torches (sticks + charcoal).
3. **FOOD**: kill cows/pigs/sheep near the table with sweep protocol (or pit-trap), cook in furnace (planks as fuel). Eat before saturation hits 0.
4. **Ruined portal** (x≈100+, z≈−60): obsidian, gold, maybe flint-and-steel. Verify location in daylight.
5. **Wool → bed**: 3 sheep via pit-trap or graze-window sweep; bed = 3 wool + 3 planks. Then nights become skippable.
6. **Iron**: cave at (174,−71) with torches → full iron, shield, armor → nether prep.
