# Game Plan — Beat the Game (No Cheats)

**Constraint**: no commands/cheats. Difficulty: **peaceful** (user will raise it later — required for blaze rods, ender pearls, string, arrows).

## Current state (STOPPED at tick ~64k, day 2 morning)

- **Pos**: (-96.4, 59, 60.9), health 20, hunger full. Player name "Prohect".
- **Advancements**: Stone Age, Getting an Upgrade.
- **Base** (trench at ~(-97.6, 59, 62.7)): crafting table + furnace placed on wall faces, 2 torches placed.
- **Hotbar**: 1 wooden axe (47/59), 2 wooden pick (30/59), 3 bread, 4 torch x2, 5 birch plank x1, 6 stone pick (~100/131), 7 dirt x3, 8 stone axe, 9 stone sword.
- **Inventory**: coal x2, torches x6 more, cobble x3, granite x5, sticks ~20, leaf litter x23, food: bread x2, apples x2, raw salmon x2. Dirt nearly spent.

### Key locations

- **Spawn**: (6.6, 64, 7.6) — ruined portal + EMPTY chest; tall stone-brick pillar (worldgen?). Lake east, forest west.
- **Forest**: west of spawn around (-100, 79, 30). Birch + dark oak + oak. Leaf litter patches everywhere.
- **Cave**: entrance ~(-98, 57, 55); interior pool at (-98, 56, 53-58); coal + granite + andesite/diorite inside. Water pool is a trap — avoid swimming.
- **Coal cliff** (north face): (-96, 59, ~57) — 5-6 coal ore at eye level, easy.
- **Vertical shaft**: (-99.67, 62.7) from y=59 down to y=44 (dirt pillar inside to climb out; granite pocket at y 51-59, plain stone below).
- **Animals seen**: cow near cave exit ~(-95, 59, 56); chickens at forest edge ~(-104, 79, 27).

## Plan (next steps in order)

1. [ ] Hunt food: cow/pigs/sheep near base; chickens in forest. Cook in furnace (coal fuel).
2. [ ] Bed: 3 wool from sheep (skip nights; set spawn).
3. [ ] Iron: branch-mine at y~50 from the shaft bottom, or explore the cave. Need ~40 iron (full armor 24 + pick 3 + bucket 3 + shield 1 + sword 2 + flint&steel 1).
4. [ ] Bucket + shield + iron armor; wheat/carrot farm optional.
5. [ ] Diamonds at y ~-59 (need 3+ for pick; more for armor/enchant table).
6. [ ] Obsidian (10) → nether portal; light with flint&steel.
7. [ ] **ASK USER to raise difficulty** (blazes/endermen don't spawn on peaceful).
8. [ ] Nether fortress → 6-8 blaze rods; endermen at night → 12+ pearls. Bow + arrows.
9. [ ] Eyes of ender → stronghold → end portal → dragon fight (bow for crystals, water bucket, beds).

## Hard-won lessons (THIS RUN, verified)

### Block placement (+use)

- **Placement works at pitch 0 on eye-level wall faces** (single `tap_use`); downward-pitch placements at the ground FAIL reliably (freeCursor mode). If placement fails, aim at a wall face at eye level and retry; never trust one press — verify held item got consumed.
- Right-clicking an existing table with +use OPENS it instead of placing (sneak not available) — aim beside it.

### Mining & drops

- **Straight-down digging >> face-mining**: continuous +attack auto-continues as you fall (7-8 blocks per 140-tick hold). Face-mining stalls after 1-2 blocks (ray finds air on retarget). Straight-down can break into caves — small falls OK (peaceful regen).
- Drops scatter 1-2 blocks; walk a small square to collect. Items float in water (collect by wading).
- **Underwater: don't.** Mining 5x slower, +jump ascent fails under ledges. If submerged: move horizontally to the known air pocket, don't just +jump.
- Aim-verify via tooltip (mod shows block name + mineable check) before long holds; swinging at out-of-reach blocks costs nothing but does nothing (check durability to confirm work done).

### Crafting / swapSlot / split (extends crafting-notes.md)

- **Peeled item from `split` goes to index-smallest EMPTY player slot at pickup time** — NOT necessarily slot 14. If you emptied slot 4 mid-chain, peels land in 4. Track the current smallest-empty; don't hardcode.
- Slot 14 keeps a "guest" stack through split staging swaps (swap semantics) — but c-grid staging swaps it INTO the grid; it round-trips safely.
- **Shaped recipes fail if ANY extra items sit in the grid** (torch craft failed with leftover sticks in c08). Clear all non-recipe slots before taking the result.
- Crafting from result slot c01: target player slot MUST be empty (silent fail otherwise). Consecutive result-crafts need different empty targets.
- 2x2 grid with planks only in c02+c03 = pressure-plate recipe; fill all 4 slots for the table. One cobble stack + splits: 7 peels for a furnace ring.
- Chain-of-swaps verify: cheap = check `logDiff` for var lines; expensive ops = getState between.

### Tool-call hygiene (user tip)

- Trailing `wait\N` is a NOP. `runAlias` returns immediately; don't burn calls on `runAlias wait\N` — use `getState` to observe between chains.
- getState/runAlias return PRE-execution snapshots; a chain started earlier is still running — check `tick` + logDiff to tell mid-chain states.

## reference

- [crafting-notes.md](./crafting-notes.md) — swapSlot semantics, split alias usage, c-slot layouts, recipes (linked from AGENTS.md)
