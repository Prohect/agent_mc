# Game Plan — beat the game 7

## Current state (session end — game closed)
- Furnace screen OPEN at (-45.7, 6, 23.7), underground mine, y6. Day ~6, health 20, hunger 20.
- **Furnace active**: 1 iron ingot ready in output (c3), 1 raw iron still smelting (c1), 4 sticks fuel remaining (c2). 3 sticks unused inventory 20 is empty, plus 2 sticks in slot 20? (now gone).
- **Hotbar**: sapling, wooden axe(20), stone pickaxe(48), dirt 36, cobble 56, furnace(placed→empty 6), wheat seeds 2, torch 2, stone sword(119).
- **Inventory highlights**: cobble 64(+18), diorite 18, cinnabar 23, andesite 14, granite 12, lapis 14, **raw iron 0 (smelting)**, raw copper 5, leaf litter 7, pumpkin 2, crafting table 1(+1 placed underground), stone axe(101), 2 stone pickaxes(4,9), stone hoe(131), 1 iron ingot(!), 0+2 sticks.
- 2nd iron ingot finishing in ~220 ticks from now.
- **Crafting table placed** 3 blocks behind at (-45, 6, 23.3). **Furnace placed** next to it.

## What happened this session (summary of the long run)
- **Died 3 times** — all drowning in magma-column lake near (28-42, y56-59). keepInventory on → items preserved.
- **Portal at (52,-20)** confirmed but unreachable via west/south water (lake full of bubble columns). North land approach untested.
- **Hunting was a failure** — chased pigs/sheep/cows for 2 game-days (day 2–5), never landed a kill, due to poor aiming via screenshots + API lag. Animals fled faster than reaction time.
- **Torches crafted** (8, smelted birch wood → charcoal → torches). Placed 3 lighting breadcrumbs.
- **Pillar-tower** + **dig-step climbing** used extensively to escape ravines/hills.
- **Horizontal mining started**: from surface nook at (-18,42,24) dug straight down to y6, then meandered toward cave sounds. Found **1 iron vein** (2 raw iron, now smelting), **copper x5**, **lapis x14**, **cinnabar** (decorative), **diorite/andesite/granite**.
- **Cave sounds** nearby (splashing, sulfur cube bounces, bats, eerie noise, lava pops) — an open water cave at ~15m to the south, with lava pool(s).

## Hard-won lessons (standalone or additions)
- **Never enter water >1 block deep**: this world has massive magma fields in lakes and flooded caves; every deep-water excursion ended in drowning.
- **Furance placement failure**: at pitch 45° targeting a block <1m away (pitch hits block I'd intersect) — backup, aim farther (>1.5m), or place on a vertical face at pitch ~0-10°.
- **Entity-blocked movement**: invisible wandering-trader/llama in a 1-wide trench can block all movement — dig through ceiling or flank differently.
- **Gravel freezes movement**: standing in a gravel pocket undergoing successive collapses pins you in place — mine downward or tower out.
- **swapSlot\6\N** works off-screen (6=hotbar), **swapSlot\N\N where N≥10 needs inventory/container screen open**? Not fully confirmed — earlier off-screen swapSlot\16\6 succeeded but later swapSlot\17\6 failed — may depend on screen state at time of call.
- **Bubble-column physics**: tried all combos; only horizontal swim away from the column center escapes. +jump is useless; sneaking makes it worse.
- **Cinnabar is non-placeable** (or difficult) — furnace wouldn't place on cinnabar floor blocks. Granite and stone work.

## Immediate next steps on resume
1. Collect 2nd iron ingot from furnace (swapSlot\c3\23).
2. Open crafting table, craft **shears** (2 iron ingots: applyRecipe\minecraft:shears, grab).
3. Return to surface (tower up through the nook hole at (-18,42,24)).
4. Find sheep on the plains (easier with daylight), right-click with shears for 1-3 wool (no aiming needed!).
5. Craft a **bed** (3 wool + 3 planks → use remaining oak planks if any, or craft more from birch wood inventory).
6. After bed: set spawn, sleep through nights, then: mine more iron for armor + shield + bucket; portal approach from north land route; diamonds at y-59.
