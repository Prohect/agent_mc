# Game Plan — Beat the Game (No Cheats)

**Constraint**: no commands/cheats. Difficulty: **peaceful** (user will raise it later — required for blaze rods, ender pearls, string, arrows). **Version: Vanilla 26.2** (confirmed by user).

## Current state (PAUSED by user, tick ~32k into session 3)

- **Pos**: (-97.3, 60, 62.2) — SURFACE, at base trench next to crafting table + furnace. Night, rain. Health 20.
- **Advancements**: Stone Age, Getting an Upgrade.

### Hotbar

1. cobble x12, 2. wooden pick (7/59), 3. bread x1, 4. empty, 5. torch x2, 6. stone pick (5/131), 7. granite x33, 8. stone axe, 9. stone sword (129/131).

### Inventory highlights

- **No iron yet.** Picks are nearly exhausted: wooden (7), stone (5). Sticks ~4. No logs.
- cobbled_deepslate ~100, cobble 12, diorite 41, granite 33, tuff 12, gravel 27, flint 3.
- cinnabar x49, sulfur x11, sulfur_spike x1 (all decorative, see RESEARCH).
- raw_copper x4, copper_ingot x1, torches ~8.
- Food: bread x2, cooked_salmon x2, apple x2.

## 🔬 RESEARCH RESULTS (vanilla 26.2, minecraft.wiki)

- **Zero iron explained**: base sits above a **Sulfur Caves** biome (new in 26.2). There, sulfur/cinnabar replace most stone/deepslate, and **"ores rarely generate here"** — iron ore only replaces stone/andesite/diorite/granite/tuff/deepslate. ~250 blocks mined in-biome → 0 iron.
- **Sulfur caves are "much smaller and more fragmented"** than normal cave biomes; small spots under hilltops; **sulfur springs on the surface indicate caves below**.
- **Iron gen (unchanged)**: peak y16 (y-24..56), uniform y-64..72, huge mountain peak y232 (y80..384). Also: iron ore loot in new **abandoned camps** structures.
- **Cinnabar / sulfur**: decorative only, NOT smeltable (verified in-game).
- **Sulfur cubes** (new mob, sulfur caves only): absorb DROPPED blocks incl. iron ore (→ "medicine ball" archetype); drop absorbed block on death or via shears. Irrelevant on peaceful.
- Furnace vanilla: wooden tool = 200t = exactly 1 smelt; stick = 100t = half smelt (wastes fuel if alone).

## Underground layout (mapped)

- **Main shaft** (-98.7, 62.7): NOW PILLAR-FILLED y16→61 (towered out). Open below y16.
- **y16 east tunnel**: x -98.6 → -74.7, z≈62.7; connects via new tower column at (-74.7, 62.65) down through cave to y-1 tunnel.
- **y16 north tunnel**: z 62.3 → 40.3 at x=-98.7.
- **y-1 tunnel**: x -85.7 → -43.3, z≈62.3-62.7. Roof holes at x≈-68.5 (pillar-filled) and x≈-73.8 (open pillar to y16).
- **y-39 shaft** (-43.3, 62.3): pillar-filled. **y-39 east tunnel**: x -43.3 → -11.3.
- **Second shaft** (-97.3, 62.7): pillar-filled.
- Messy pockets x -85..-105, y -8..2 — disorienting; also a tunnel at y≈-4/-6 z≈61.3 x -66..-95.

## Plan (next steps in order)

1. [ ] **Wood run**: forest at (-100, 79, 30) — logs for sticks/torches/chests; stone axe (129/131) works.
2. [ ] Craft stone picks ×4+ at base.
3. [ ] **Escape the sulfur-caves biome**: new shaft+branch at y16 ≥ 60-100 blocks away from base (try far west past forest, or far north; avoid surface sulfur springs). Mine until OUT of cinnabar/sulfur rock (plain stone) → iron.
4. [ ] Iron ~40 → iron gear (armor 24, pick 3, bucket 3, shield 1, sword, flint&steel 1 — have flint x3).
5. [ ] Diamonds y-59 (3+ for pick). Obsidian 10 → portal, light it.
6. [ ] **ASK USER to raise difficulty** (blazes/endermen need non-peaceful).
7. [ ] Nether fortress → blaze rods; endermen → pearls; bow + arrows.
8. [ ] Eyes of ender → stronghold → dragon.

## Run-specific lessons (general knowledge lives in the note files)

- **Cave water near base is extensive** — pool extends east under the dirt mound (-91.7, 60, 63.7). Fell through at (-85.3, 60, 65.7) → y54 water, nearly drowned. Escape: air pocket (-89.7, 55, 55.6); terraces NW → grass gap yaw 115. **Do not dig hillside walls in cave-adjacent dirt.**
- **Climbing 2-high ledges**: mine bottom (pitch 0), mine top (pitch -60/-65), then forward+jump. Jump alone clears only 1.
- **Peeled items from `split` go to index-smallest EMPTY player slot** — re-check `empty_inv` each time, don't hardcode.
- **3 cobble in a row = SLAB recipe** — add sticks BEFORE taking result (else you craft slabs).
- **swapSlot from result slot c01 requires an EMPTY target player slot** (silent fail otherwise); refill c09 with single sticks between pick crafts.
- For mining/bridging timing & geometry, signatures, and tool-call rules see the reference files below.

## reference

- [mining-notes.md](./mining-notes.md) — block×tool timing, benchmarks, ore detection
- [bridging-notes.md](./bridging-notes.md) — placement geometry, bridge & stair-bridge recipes
- [diagnostics.md](./diagnostics.md) — state signatures, mismatch→cause, tool-call hygiene
- [crafting-notes.md](./crafting-notes.md) — swapSlot semantics, split alias, c-slot layouts, recipes
