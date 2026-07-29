# Game Plan — "beat the game 7"

Goal: beat the Ender Dragon.

## Current state (start of day 4, paused in PauseScreen)

- Position: `x≈174.3, y≈64, z≈-71.3` — sealed inside a 1×1 tunnel in a dirt/stone hill (sheep hills, east of spawn plains). Safe.
- Health 20, hunger 20. **No food, no logs.** Stone pickaxe at ~118/131.
- Hotbar: 1 sapling, wooden axe (46), **stone pickaxe**, 18 dirt, 6 cobble, **furnace**, wheat seeds, **stone axe**, **stone sword**. 3 sticks in inventory.
- Advancements: Stone Age, Getting an Upgrade.

## Landmarks

- **Crafting table**: placed in open plains at `x≈66, z≈-65`, next to a stripped-oak stump. ~110 blocks west of current position.
- **Ruined portal**: visible from the plains, roughly `x≈100+, z≈-60` area (seen east of the table, on a rise by the water). Unlooted.
- **Lake/river**: west of the plains (`x<55`), and a river gorge at `z≈-105..-114` with a dirt/stone cliff on the south bank — **DROWNING TRAP** (underwater overhang, see lessons).
- **Sheep hills**: `x≈150–190, z≈-55..-85` — sheep + cows seen repeatedly; exposed stone faces; small cave opening at `x≈174, z≈-71` (below my tunnel floor, unexplored).
- **Dark forest**: far, `x≈75–82, z≈-40..-58` per old notes (unverified).

## Hard-won lessons

### Yaw / bearings (cost me hours)
- MC yaw: **0 = south (+z), 90 = west (−x), ±180 = north (−z), −90 = east (+x)**.
- **Sound directions like `[Sheep baahs yaw+60]` are RELATIVE: bearing = current yaw + offset. Positive offset = screen-RIGHT; to face it, ADD the offset to yaw.** Verified: turning `yaw\100` toward a `yaw+100` baah put the target dead ahead.
- Check `pos` after every move — heading errors compound fast.

### Wood / drops
- **Only chop a trunk from ≤1 m (point blank).** Breaking from 2.5–3 m flings drops out of pickup range; I lost ~6 logs that way. Best: stand inside/at the trunk base, break the eye-level log, then look up/down and break the next — drops fall ON you.
- `applyRecipe` only stages the grid — must `swapSlot\c1\<dst>` to take the result. Result slot is `c1` in both 2×2 and 3×3.
- **Axe `tap_use` on a log STRIPS it** (stripped log ≠ fuel/planks? — it is usable, but unexpected). Don't hold an axe when trying to open/place things.
- Tall grass eats attacks: clear it or aim past it. Breaking grass costs no tool durability — use durability deltas to tell real breaks from grass breaks.

### Water
- Rivers can have underwater cliff overhangs: if position freezes while swimming and breath drops, you're nose-under an overhang. **Back out horizontally while jumping; never mash forward.**
- Underwater mining is ~5× slower — a 60-tick `+attack` won't finish a stone block. Don't rely on mining an air pocket; move instead.
- The `z≈-105..-114` gorge: cross it where banks are low, or pillar/bridge. South bank cliff = death trap.

### Movement
- `+hop` (jump+forward+sprint) crosses plains well but overshoots targets; check pos after.
- Digging a tunnel: the 45°-down dig leaves a lip you can't walk over — use `+hop` to enter, or dig the floor block too.
- `+loop_tower` (jump + place beneath) works when nothing is blocking above.

### Survival routine
- Night in the open with only a stone sword = wasted time. Dig a 3-deep hole BEFORE dusk, cap with dirt, nap in 60.0 s chunks (`nap` > ~50 s risks context-server timeout, but the game keeps running anyway).

## Next actions (priority order)

1. **Morning: exit tunnel, find sheep on the surrounding hills** (baahs heard repeatedly within 15 m). Kill 3 sheep → wool; keep 1–2 mutton.
2. **Chop wood properly** (point-blank rule). Need ≥6 logs: bed (3 planks + 3 wool), sticks, fuel.
3. Craft a **bed** at the plains crafting table (or craft a new table on the spot — 4 planks) → nights become skippable.
4. Place **furnace**, cook mutton (fuel: planks). Stabilize food.
5. Loot the **ruined portal** (obsidian, gold, maybe flint-and-steel).
6. Then: full iron (mine the cave under the tunnel at `x174, z-71` with torches), shield, armor → nether prep.
