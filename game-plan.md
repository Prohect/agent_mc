# Game Plan — Beat the Game (No Cheats)

**Constraint**: no commands/cheats. Difficulty: **peaceful** (user will raise it later — required for blaze rods, ender pearls, string, arrows).

## Current state (PAUSED at tick ~78k, day ~4)

- **Pos**: (-95.3, -4, 61.5), yaw -90, pitch 55, health 20, hunger 🟢. Paused mid-mine.
- **Advancements**: Stone Age, Getting an Upgrade.
- **Location**: underground in the messy pocket zone west of the main shaft, mining east toward (-66, 2) to tower up the open cave shaft I previously fell down from the y16 tunnel.

### Hotbar

1. cobbled_deepslate x12, 2. wooden pick (low), 3. bread x1, 4. **stone pick (43/131, selected)**, 5. torch x3, 6. stone pick (5/131), 7. granite x29, 8. stone axe, 9. stone sword (129/131).

### Inventory highlights

- **NO IRON** (~250 blocks mined at y16, y-1, y-39 — zero iron ore; see IRON PROBLEM below).
- Picks left: slot 31 stone pick (54/131), slot 4 (43), slot 6 (5), wooden (~14). That's it.
- cobble x59, cobbled_deepslate x64(+25→12 placed), diorite x41, granite x29, tuff x12.
- **cinnabar x42** (red cave rock, modded), **sulfur x2**, **sulfur_spike x1** (modded).
- raw_copper x5, coal 0, torches 3+2+4 elsewhere, flint x3, gravel x12.
- Food: bread x2, cooked_salmon x2, apple x2. sticks ~5, planks x2 (oak+birch).
- leaf_litter x23, andesite x10.

## ⚠️ THE IRON PROBLEM (ask user)

~250 blocks of 1x2 branch tunnel at y=16, y=-1, AND y=-39 produced **zero iron ore** (only one copper ore at y16, plus modded cinnabar/sulfur). Advancement tab is vanilla. Either very unlucky (~5%) or the modpack changed/relocated iron gen. **Plan**: surface, then **ASK USER how iron generates in this pack**. Also test: smelt 1 cinnabar + 1 sulfur in furnace to learn what they are.

## Underground layout (mapped)

- **Main shaft** (-98.7, 62.7): open y16→y60 surface (pillar re-mined away). Bottom of shaft = y16.
- **y16 east tunnel**: x -98.6 → -78.3, z≈62.7, then a mined slope descends (x -78→-66) down to y≈2 — **open cave shaft at (-66.4, 2) with my torch**; tower up there to return.
- **y16 north tunnel**: z 62.3 → 40.3 at x=-98.7.
- **y-1 tunnel**: x -85.7 → -43.3, z≈62.3-62.7.
- **y-39 shaft** (-43.3, 62.3): now pillar-filled (towered out).
- **y-39 east tunnel**: x -43.3 → -11.3, z≈62.3.
- **Second shaft** (-97.3, 62.7): dug y59→42 for cobble, then pillar-filled (towered out).
- Messy irregular pockets x -85..-105, y -8..2 (from slope-mining) — avoid, disorienting.
- Modded cave biome: red **cinnabar** rock + yellow sulfur/fungus blocks around y 0-20 near base; seems to replace vanilla ore gen locally.

## Return-to-surface route (current objective)

1. From (-95.3, -4, 61.5) continue east to (-66.4, 2) — mine through walls as needed.
2. Tower up the open cave shaft at (-66.4, 2) → y16 tunnel.
3. Walk west to main shaft (-98.7, 16) → walk up... shaft is open 16→60: tower up it (needs ~44 blocks; have cobble 59).
4. Surface: smelt-test cinnabar/sulfur; ASK USER about iron.

## Plan (next steps in order)

1. [x] Cook salmon x2 (done, in inv)
2. [ ] Return to surface (route above)
3. [ ] Smelt-test cinnabar + sulfur
4. [ ] **ASK USER: where/how does iron generate in this modpack?**
5. [ ] Iron (~40) → full iron gear → diamonds y-59 → obsidian → nether
6. [ ] **ASK USER to raise difficulty** (blazes/endermen need non-peaceful)
7. [ ] Nether fortress → blaze rods; endermen → pearls; bow + arrows
8. [ ] Eyes of ender → stronghold → dragon

## Hard-won lessons (THIS RUN, verified)

### Block placement (+use)

- Placement works at pitch 0 on eye-level wall faces (single `tap_use`); downward-pitch ground placements FAIL reliably (freeCursor). Verify held item got consumed.
- Right-clicking an existing table/furnace with +use OPENS it — aim beside it.

### Mining & drops

- **Straight-down digging >> face-mining**: continuous +attack auto-continues as you fall. Face-mining stalls; raycast needs tooltip verification.
- **Branch-mine pattern that works**: yaw fixed, `+attack +forward` held, alternate `setPitch\0 wait\30 setPitch\55 wait\30` — advances ~1 block per full cycle, BUT pitch-55 can mine down-slopes into caves (descended y16→y2 twice unintentionally).
- Deepslate mining with stone pick: ~21-30 ticks/block (slow).
- **Mining straight up (pitch -90) does NOT work** (no tooltip/no raycast hit with freeCursor) — use `+auto_tower` in open columns instead.
- **auto_tower drifts**: over long rises it can slide off-column (especially near walls); it also FILLS the shaft behind you — re-descending a shaft means re-mining your own pillar (nice for cobble restock).
- **auto_tower stops at ceilings** — check y after; if stuck, you're under rock.
- Drops scatter 1-2 blocks; walk a square to collect. Underwater: don't.
- Don't mine with sword (2 dura/block). Wooden pick CAN mine stone/coal/cobble (not iron).

### Crafting / swapSlot / split

- **3 cobble in a row = SLAB recipe** — add the sticks BEFORE taking result; result slot shows slabs otherwise.
- Stone pickaxe x3 from one 9-cobble stack: swapSlot 9-stack→c02, `var\src\c02 var\cnt\2 split` ×2 (peel 3 → smallest-empty player slot), move peels to c03/c04; sticks stack → c06, refill c09 with single sticks between crafts. Result→EMPTY player slots only.
- Peeled items go to index-smallest EMPTY player slot — re-check `empty_inv` each time, don't hardcode.
- swapSlot is pure whole-stack swap, never merges; wait\1-2 between swaps.

### Tool-call hygiene

- **Never lead a chain with `esc` after a screen-open tool call — the whole chain silently no-ops.** Use standalone `closeScreen`, then a separate call for the action chain. (Bit me 3 times.)
- Opening the inventory seems to stall mid-chain movement/mining — treat screen transitions as chain boundaries.
- Trailing `wait\N` is a NOP; runAlias returns immediately; use delay param + getState to observe.
- getState/runAlias return PRE-execution snapshots.

## reference

- [crafting-notes.md](./crafting-notes.md) — swapSlot semantics, split alias usage, c-slot layouts, recipes (linked from AGENTS.md)
