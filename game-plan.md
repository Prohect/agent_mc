# Game Plan — beat the game 7

## Current state (session end — paused)
- Game is paused at approximately `(-83.7, 64, -0.85)`, overworld surface, health recently full after respawn. CFG aliases are loaded.
- The player died once during this session after falling into lava while escaping the underground sulfur/water cave; respawned near `(-2.5, 63, -6.5)`.
- Current hotbar: oak sapling, wooden axe, empty slot 3, dirt 29, cobblestone 64, shears, wheat seeds 2, torch 2, stone sword.
- Shears remain available in hotbar slot 6; stone sword is in slot 9. Stone pickaxes broke during the escape attempt.
- Current area is open surface terrain. Pig sounds were heard during exploration; no sheep found yet.

## What happened this session
- Loaded the migrated `bind-alias.cfg` successfully with `reloadCFG`; aliases including `hop`, `loop_tower`, and `split` are active.
- Collected the second iron ingot from the furnace and crafted shears using `applyRecipe\shears`.
- Dug and navigated an upward tunnel, but repeatedly entered flooded sulfur caves and became disoriented.
- Used the remaining stone pickaxes and eventually broke them while mining stone, diorite, sulfur, and other blocks.
- Fell through a deep underground/lava area and died; respawn preserved most hotbar items, but the pickaxes were already broken.
- Respawned at the surface and traveled across open terrain. The game is now paused.

## Hard-won lessons
- **Never enter water >1 block deep**: this world has massive magma fields in lakes and flooded caves; every deep-water excursion ended in drowning or severe danger.
- **Furnace placement failure**: at pitch 45° targeting a block <1m away (pitch hits block I'd intersect) — backup, aim farther (>1.5m), or place on a vertical face at pitch ~0-10°.
- **Entity-blocked movement**: invisible wandering-trader/llama in a 1-wide trench can block all movement — dig through ceiling or flank differently.
- **Gravel freezes movement**: standing in a gravel pocket undergoing successive collapses pins you in place — mine downward or tower out.
- **swapSlot\6\N** works off-screen (6=hotbar), **swapSlot\N\N where N≥10 needs inventory/container screen open**? Not fully confirmed — earlier off-screen swapSlot\16\6 succeeded but later swapSlot\17\6 failed — may depend on screen state at time of call.
- **Bubble-column physics**: tried all combos; only horizontal swim away from the column center escapes. +jump is useless; sneaking makes it worse.
- **Cinnabar is non-placeable** (or difficult) — furnace wouldn't place on cinnabar floor blocks.
- **CFG key-release caution**: chained `wait` calls can leave movement keys held; explicitly run `-hop`, `-forward`, `-jump`, and `-sprint` before changing direction or opening screens.
- **Towering**: `+loop_tower` can place blocks when aimed down at a valid supporting block, but it is unsafe near ravines and can carry stale movement keys. Stop movement before using it.

## Immediate next steps on resume
1. Keep the game on the surface and avoid caves, deep water, lava, and sulfur hazards.
2. Explore plains/grassland during daylight with shears equipped; locate sheep and shear them rather than chasing animals.
3. Collect at least 3 wool, then craft a bed using available wood. Set spawn and sleep.
4. Obtain a replacement pickaxe before returning underground; use surface wood and stone if necessary.
5. After a bed: gather iron for armor, shield, bucket, and tools; approach the ruined portal at `(52, -20)` from the north land route.
6. Do not resume the old furnace/tunnel plan until confirming whether the old underground supplies remain accessible; the current player is safely on the surface.
