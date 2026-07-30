# Game Plan — beat the game 7

## Current state (session end)
- Paused at (-180.4, 81, 9.4), overworld, dawn of ~day 2, health 20, hunger 20.
- Hotbar: oak sapling, wooden axe (38/59), stone pickaxe (80/131), dirt ~30, cobblestone 6, furnace, wheat seeds, stone axe (101/131), stone sword (131/131).
- Inventory: 2 crafting tables, 8 sticks, 8+4 oak planks, 4 birch planks, 3 birch wood, 2 pumpkins, leaf litter, oak log(s) may be gone (crafted).
- Standing at forest edge on a hill; open plains with sheep/pigs visible to yaw ~200 (south-west-ish), ~10m ahead.

## What happened this session
- Started at spawn (69, -61) during night thunderstorm with basic kit; rain stopped at dawn.
- Found **ruined portal in the water at ~(52, -20)** — lava + gold blocks visible, NOT yet looted (chest likely). Marked as key landmark.
- Nearly drowned repeatedly in a **magma bubble column near (33, -3, y56)** — +jump could not overcome the pull; escaped by pure +forward horizontal swimming. LESSON: in bubble columns, swim sideways out, don't fight upward.
- Looted **pumpkins** (5-6 mined) on the hills around (-35, -24).
- Fell into a ravine at ~(-80, 0) with **exposed coal ore** on walls (unmined). Escaped via dirt-pillar + dig-step cycles.
- Reached forest at night (~(-170, -15)), chopped 1 birch + 1 oak → 3 oak logs, 1 stripped oak log, ~5 birch logs.
- Crafted: 2 crafting tables, 8 sticks, 12 oak planks, 4 birch planks, 3 birch wood (accidental "wood" craft — see lesson).
- Morning of day 2: heading back toward plains to hunt pigs/cows/sheep for food & wool.

## Immediate next steps
1. Hunt sheep (need 3 wool for bed) + pigs/cows (food). Sheep seen at plains edge, pigs at (-76, -37) valley.
2. Place furnace, smelt birch wood → charcoal (planks as fuel) → craft torches.
3. Loot ruined portal at (52, -20) — gold blocks + possible chest (flint&steel/obsidian chance).
4. Mine coal in ravine at (-80, 0) for more torches.
5. Bed before next night (sleep through thunderstorms).

## Hard-won lessons (new this session)
- **Movement math**: forward vector = (-sin(yaw), +cos(yaw)) in (x,z). Verified repeatedly.
- **Bubble columns**: +jump loses to magma pull; escape horizontally (+forward/+sprint only).
- **Water exits**: +forward++jump at a shore works only if the bottom slopes up; against a 2-deep wall it fails — pillar or dig instead.
- **loop_tower fails under a ceiling** (blocks can't place, wastes nothing but time) and while standing on pillar edge — check sky first (screenshot) before towering.
- **Dig-step climbing**: attack eye-level block, then +hop — reliable 2-high wall climb; ~+1y per cycle. Pickaxe for stone walls.
- **applyRecipe matches substrings loosely**: "birch planks" matched "Birch Wood" (4 logs→3 wood, wasteful). Use full ids: `minecraft:birch_planks`.
- **Container slots are c1-based, not c0** — result slot of 2x2 inventory grid is `c1`, grid is c2-c5. Grab result via `swapSlot\c1\N`.
- **Recipe grab**: after applyRecipe, result stack goes to cursor; must swapSlot it into a numbered slot or it lingers on cursor ("stack remains on cursor" warnings).
- Ravines/ditches in hilly flower forest are everywhere when sprint-hopping — "Something trips/Player hurts" means you fell; check y before continuing.
